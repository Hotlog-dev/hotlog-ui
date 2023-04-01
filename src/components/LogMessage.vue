<template>
  <div class="hotlog--log">
    <div :class="`lsn-type--${messageType}`" class="typeof-log">{{ messageType }}</div>
    <div :class="`lsn-type--${messageType}`" ref="log-value" class="valueof-log">
      <div ref="logViewer" class="log-viewer">
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import {onMounted, ref} from "vue";
// @ts-ignore
import JsonViewer from '../lib/json-viewer/json-viewer.js'
let props = defineProps(['message']
)
const logViewer = ref<HTMLElement | null>(null)
const messageType = ref<string | object |number | [] |null >(null)
onMounted(() => {
  if(messageType.value && logViewer.value){
    messageType.value = typeof props.message
    switch (typeof props.message) {
      case 'object':
        logViewer.value.innerHTML = '';

        new JsonViewer(props.message, logViewer.value)
        break;
      case 'string':
        logViewer.value.innerHTML = props.message.toString().trim();
        break;
    }
  }

})
</script>
<style lang="scss">
.hotlog--log {
  display: flex;
  align-items: center;
  color: var(--hotlog-log-text);
  min-height: 40px;
  padding: 5px 10px;

  &.group-hightlight {
    background: #151823;
  }

  .idof-log {
    text-align: center;
    min: 80px;
    max-width: 128px;
    font-size: 10px;
    margin-right: 10px;
    overflow: hidden;
    overflow-wrap: break-word;
    white-space: pre-wrap;
  }

  .dateof-log {
    text-align: center;
    min: 80px;
    max-width: 128px;
    font-size: 10px;
    margin-right: 10px;
    overflow: hidden;
    overflow-wrap: break-word;
    white-space: pre-wrap;
  }

  .levelof-log {
    text-align: center;
    flex: 0 0 50px;
    font-size: 10px;
    margin-right: 10px;
  }

  .typeof-log {
    text-align: left;
    flex: 0 0 50px;
    font-size: 10px;
    margin-right: 10px;
  }

  .valueof-log {
    flex: 1;
    text-align: left;
    margin: 0 10px;
    font-size: 14px;
    overflow: hidden;
    overflow-wrap: break-word;
    white-space: pre-wrap;

    .log-viewer {
      .lsn-json-viewer {
        font-size: smaller;
        background: var(--hotlog-log-background);
        padding: 0.5rem;
        border-radius: 0.5rem;
        overflow: auto;
        color:var(--text-color);
        max-height: unset;
        .lsn-json-entry-left{
          color:var(--hotlog-log-text);
        }
      }
    }

    &.string-log {
      .log-viewer {
        &:before, &:after {
          content: '"';
        }

      }
    }

  }
}
</style>
