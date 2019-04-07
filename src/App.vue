<template>
  <div id="app">
    <div id="content">
      <!--<BScroll>-->
      <!--<div>-->
      <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
      <!--</div>-->
      <!--</BScroll>-->
      <ResumeEditor
        ref="resumeEditor"
        :markdown="currentMarkdown"
        :enableHtml="enableHtml"
      ></ResumeEditor>
    </div>
    <div id="foot">
      <ThankEditor
        ref="thankEditor"
        :markdown="currentThankMarkdown"
        :enableHtml="enableHtml"
      ></ThankEditor>
    </div>
  </div>
</template>

<script>
import StyleEditor from "@/components/StyleEditor";
import ResumeEditor from "@/components/ResumeEditor";
import ThankEditor from "@/components/ThankEditor";
// import BScroll from "@/components/BScroll";

import { fullStyle, fullMarkdown, thanksMarkdown } from "@/utils";

export default {
  name: "app",
  components: {
    StyleEditor,
    ResumeEditor,
    ThankEditor
    // BScroll
  },
  data() {
    return {
      interval: 10,
      currentStyle: "",
      enableHtml: false,
      fullStyle: fullStyle,
      currentMarkdown: "",
      currentThankMarkdown: "",
      fullMarkdown: fullMarkdown,
      thanksMarkdown: thanksMarkdown
    };
  },
  methods: {
    async makeResume() {
      await this.progressivelyShowStyle(0);
      await this.progressivelyShowResume();
      await this.progressivelyShowStyle(1);
      await this.showHtml();
      await this.progressivelyShowStyle(2);
      await this.progressivelyShowThanks();
      await this.progressivelyShowStyle(3);
    },
    showHtml() {
      return new Promise(resolve => {
        this.enableHtml = true;
        resolve();
      });
    },
    progressivelyShowStyle(n) {
      return new Promise(resolve => {
        let interval = this.interval;
        let showStyle = async function() {
          let style = this.fullStyle[n];
          if (!style) {
            return;
          }
          // 计算前 n 个 style 的字符总数
          let length = this.fullStyle
            .filter((_, index) => index <= n)
            .map(item => item.length)
            .reduce((p, c) => p + c, 0);
          let prefixLength = length - style.length;
          if (this.currentStyle.length < length) {
            let l = this.currentStyle.length - prefixLength;
            let char = style.substring(l, l + 1) || " ";
            this.currentStyle += char;
            if (style.substring(l - 1, l) === "\n" && this.$refs.styleEditor) {
              this.$nextTick(() => {
                this.$refs.styleEditor.goBottom();
              });
            }
            setTimeout(showStyle, interval);
          } else {
            resolve();
          }
        }.bind(this);
        showStyle();
      });
    },
    progressivelyShowResume() {
      return new Promise(resolve => {
        let length = this.fullMarkdown.length;
        let interval = this.interval;
        let showResume = () => {
          if (this.currentMarkdown.length < length) {
            this.currentMarkdown = this.fullMarkdown.substring(
              0,
              this.currentMarkdown.length + 1
            );
            let prevChar = this.currentMarkdown[
              this.currentMarkdown.length - 2
            ];
            if (prevChar === "\n" && this.$refs.resumeEditor) {
              this.$nextTick(() => this.$refs.resumeEditor.goBottom());
            }
            setTimeout(showResume, interval);
          } else {
            resolve();
          }
        };
        showResume();
      });
    },
    progressivelyShowThanks() {
      return new Promise(resolve => {
        let length = this.thanksMarkdown.length;
        let interval = this.interval;
        let showThanks = () => {
          if (this.currentThankMarkdown.length < length) {
            this.currentThankMarkdown = this.thanksMarkdown.substring(
              0,
              this.currentThankMarkdown.length + 1
            );
            let prevChar = this.currentThankMarkdown[
              this.currentThankMarkdown.length - 2
            ];
            if (prevChar === "\n" && this.$refs.thankEditor) {
              this.$nextTick(() => this.$refs.thankEditor.goBottom());
            }
            setTimeout(showThanks, interval);
          } else {
            resolve();
          }
        };
        showThanks();
      });
    }
  },
  created() {
    this.makeResume();
  }
};
</script>
