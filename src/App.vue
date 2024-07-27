<script setup lang="ts">
import {getDocument, GlobalWorkerOptions} from "pdfjs-dist";
import {ref, onMounted, onUnmounted} from "vue";
GlobalWorkerOptions.workerSrc = '/pdf.worker.mjs';
let pdfDoc: any = null;
let pageNumPending: number | null = null;

onMounted(() => {
  window.addEventListener('scroll', handleScroll);
});
onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
});

const handleScroll = () => {
  const scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
  const scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight;
  const clientHeight = document.documentElement.clientHeight || document.body.clientHeight;
  remainingToBottom.value = scrollHeight - scrollTop - clientHeight;
};

const remainingToBottom = ref<number>(0);
const renderPage = async (num: number) => {
  const page = await pdfDoc.getPage(num);
  const viewport = page.getViewport({scale: 1});

  const canvas = document.createElement('canvas');
  const context = canvas.getContext("2d")!;
  const containerWidth = document.getElementById("pdf-container")!.getBoundingClientRect().width;
  const scale = containerWidth / viewport.width;
  canvas.width = containerWidth;
  canvas.height = viewport.height * scale;

  const renderContext = {
    canvasContext: context,
    viewport: page.getViewport({scale: scale}),
  };
  const renderTask = page.render(renderContext);
  await renderTask.promise;

  document.getElementById("pdf-container")!.appendChild(canvas);

  if (pageNumPending !== null) {
    await renderPage(pageNumPending);
    pageNumPending = null;
  }
};
const pdfUrl = ref<string>("/demo.pdf");
const renderAllPages = async () => {
  for (let i = 1; i <= pdfDoc.numPages; i++) {
    await renderPage(i);
  }
};

const loadPdf = async () => {
  const loadingTask = getDocument(pdfUrl.value);
  pdfDoc = await loadingTask.promise;
  await renderAllPages();
};
loadPdf();

</script>

<template>
  <div id="pdf-container">
  </div>
</template>

<style scoped>
#pdf-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-width: 800px;
  width: 100%;
}
</style>
