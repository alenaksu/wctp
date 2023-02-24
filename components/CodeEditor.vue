<script setup lang="ts">
import { useEventListener } from '@vueuse/core';
import { ref } from 'vue';

const js = ref<HTMLElement>();
const html = ref<HTMLElement>();
const preview = ref<HTMLElement>();
const code = ref({
  html: '',
  js: ''
});

const updateDelay = 1000;
let updateTimerId = 0;

const updatePreview = () => {
    clearTimeout(updateTimerId);
    updateTimerId = setTimeout(() => {
        const { html, js } = code.value;
        preview!.value!.setAttribute(
            'srcdoc',
            `
              <html>
                <body>
                  ${html}
                  ${'<'}script type="module">${js}${'<'}/script>
                </body>
              </html>
            `
        );
    }, updateDelay);
};

useEventListener(window, 'message', (message) => {
  const frame = (message?.source as any).frameElement;
  const payload = message.data;
  const htmlFrame = (html!.value! as any).$refs.iframe as HTMLIFrameElement;
  const jsFrame = (js!.value! as any).$refs.iframe as HTMLIFrameElement;

  if (payload.type !== 'slidev-monaco') return;

  if (frame === htmlFrame) {
    code.value.html = payload.data.code;
  } else if (frame === jsFrame) {
    code.value.js = payload.data.code;
  }

  updatePreview();
})

</script>

<template>
  <div class="grid grid-cols-2 grid-rows-1 gap-8 h-full">
    <div class="flex flex-col gap-6 -mt-6">
      <div class="">
        <small>HTML</small>
        <div class="resize-y overflow-auto">
          <Monaco ref="html" code="" lang="html" class="h-full" height="100%" />
        </div>
      </div>
      <div class="flex flex-col h-full">
        <small>JavaScript</small>
        <Monaco ref="js" code="" lang="js" class="h-full" height="100%" />
      </div>
    </div>
    <iframe ref="preview" class="text-base w-full h-full rounded"></iframe>
  </div>
</template>
