<script lang="ts">
import Vue from 'vue';
import { createEditor } from '@wangchaodeyuzhou/editor';

function genErrorInfo(fnName: string) {
  let info = `请使用 '@${fnName}' 事件，不要放在 props 中`;
  info += `\nPlease use '@${fnName}' event instead of props`;
  return info;
}
// @ts-ignore
export default Vue.extend({
  //【注意】单独写 <template>...</template> 时，rollup 打包完浏览器运行时报错，所以先在这里写 template
  render(h: Function) {
    return h('div', { ref: 'box' });
  },
  name: 'Editor',
  data() {
    return {
      curValue: '',
      editor: null,
    };
  },
  props: ['defaultContent', 'defaultConfig', 'mode', 'defaultHtml', 'value'], // value 用于自定义 v-model
  mounted() {
    this.create();
  },
  watch: {
    // 监听 'value' 属性变化 - value 用于自定义 v-model
    value(newVal: any) {// @ts-ignore
      const isEqual = newVal === this.curValue;
      if (isEqual) return; // 和当前内容一样，则忽略

      // 重置 HTML
      // @ts-ignore
      this.setHtml(newVal);
    },
  },
  methods: {
    // 重置 HTML
    setHtml(newHtml: string) {// @ts-ignore
      const editor = this.editor as any;
      if (editor == null) return;
      editor.setHtml(newHtml);
    },

    // 创建 editor
    create() {// @ts-ignore
      if (this.$refs.box == null) return;
     // @ts-ignore
      const defaultConfig = this.defaultConfig || {};

      const defaultContent = JSON.stringify(// @ts-ignore
        Array.isArray(this.defaultContent) ? this.defaultContent : []
      );

      createEditor({// @ts-ignore
        selector: this.$refs.box as Element,// @ts-ignore
        html: this.defaultHtml || this.value || '',// @ts-ignore
        config: {
          ...defaultConfig,
          onCreated: (editor) => {// @ts-ignore
            this.editor = Object.seal(editor) as any; // 注意，一定要用 Object.seal() 否则会报错
            // @ts-ignore
            this.$emit('onCreated', editor);
            if (defaultConfig.onCreated) {
              const info = genErrorInfo('onCreated');
              throw new Error(info);
            }
          },
          onChange: (editor) => {
            const editorHtml = editor.getHtml();// @ts-ignore
            this.curValue = editorHtml; // 记录当前 html 内容
            // @ts-ignore
            this.$emit('input', editorHtml); // 用于自定义 v-model
            // @ts-ignore
            this.$emit('onChange', editor);
            if (defaultConfig.onChange) {
              const info = genErrorInfo('onChange');
              throw new Error(info);
            }
          },
          onDestroyed: (editor) => {// @ts-ignore
            this.$emit('onDestroyed', editor);
            if (defaultConfig.onDestroyed) {
              const info = genErrorInfo('onDestroyed');
              throw new Error(info);
            }
          },
          onMaxLength: (editor) => {// @ts-ignore
            this.$emit('onMaxLength', editor);
            if (defaultConfig.onMaxLength) {
              const info = genErrorInfo('onMaxLength');
              throw new Error(info);
            }
          },
          onFocus: (editor) => {// @ts-ignore
            this.$emit('onFocus', editor);
            if (defaultConfig.onFocus) {
              const info = genErrorInfo('onFocus');
              throw new Error(info);
            }
          },
          onBlur: (editor) => {// @ts-ignore
            this.$emit('onBlur', editor);
            if (defaultConfig.onBlur) {
              const info = genErrorInfo('onBlur');
              throw new Error(info);
            }
          },
          customAlert: (info, type) => {// @ts-ignore
            this.$emit('customAlert', info, type);
            if (defaultConfig.customAlert) {
              const info = genErrorInfo('customAlert');
              throw new Error(info);
            }
          },
          customPaste: (editor, event) => {
            if (defaultConfig.customPaste) {
              const info = genErrorInfo('customPaste');
              throw new Error(info);
            }
            let res;// @ts-ignore
            this.$emit('customPaste', editor, event, (val: any) => {
              res = val;
            });
            return res;
          },
        },
        content: JSON.parse(defaultContent),// @ts-ignore
        mode: this.mode || 'default',
      });
    },
  },
});
</script>
