<script lang="ts">
import Vue from 'vue';
import { createToolbar, DomEditor } from '@wangchaodeyuzhou/editor';
// @ts-ignore
export default Vue.extend({
  name: 'Toolbar',
  render(h: Function) {
    return h('div', { ref: 'box' });
  },
  props: ['editor', 'defaultConfig', 'mode'],
  methods: {
    // 创建 toolbar
    create(editor: any) {
      // @ts-ignore
      if (this.$refs.box == null) return;
      if (editor == null) return;
      if (DomEditor.getToolbar(editor)) return // 不重复创建

      createToolbar({
        editor,// @ts-ignore
        selector: this.$refs.box as Element,// @ts-ignore
        config: this.defaultConfig || {},// @ts-ignore
        mode: this.mode || 'default',
      });
    },
  },
  watch: {
    editor: {
      handler(e: any) {
        if (e == null) return;// @ts-ignore
        this.create(e);
      },
      immediate: true,
    }
  }
});
</script>
