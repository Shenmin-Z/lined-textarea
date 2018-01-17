<template>
    <div class="lined-textarea">
        <div class="lined-textarea__lines"
             :style="{ 'padding-right': longestWidth + 'px'}"
        >
            <div class="lined-textarea__lines__inner"
                 ref="lines"
            >
                <p v-for="(line, index) in lines"
                   :key="index"
                   class="lined-textarea__lines__line"
                   :class="{ 'lined-textarea__lines__line--invalid': invalidLines.includes(line) }"
                   v-html="line"
                ></p> 
            </div>
        </div>
        <textarea :disabled="disabled"
                  class="lined-textarea__content"
                  v-model="content"
                  v-on:scroll="scrollLines"
                  :style="styles"
                  ref="textarea"
        ></textarea>
        <div class="count-helper" ref="helper"></div>
    </div>
</template>

<script>
export default {
    name: 'LinedTextarea',
    mounted() {
        this.syncScroll();
        this.calculateCharactersPerLine();
    },
    data() {
        return {
            content: '',
            widthPerChar: 8, // Hard coded
            numPerLine: 1,
            scrolledLength: 0
        };
    },
    props: {
        validate: {
            type: Function,
            default: () => true
        },
        disabled: {
            type: Boolean,
            default: false
        },
        placeholder: {
            type: String,
            default: ''
        },
        styles: {
            type: Object,
            default: () => {
                return {
                    height: '300px'
                };
            }
        }
    },
    computed: {
        invalidLines() {
            const lineNumbers = [];
            this.content.split('\n').forEach((line, index) => {
                if (!this.validate(line)) {
                    lineNumbers.push(index + 1);
                }
            });
            return lineNumbers;
        },
        lines() {
            if (this.content === '') return [1];
            const lineNumbers = [];
            let num = 1;
            function getWrapTimes(sentence, width) { // Seems to work with pre-wrap (has problem with dash)
                const words = sentence.split(' ');
                let currentLine = 1;
                let spaceLeft = width;
                words.forEach((word) => {
                    while (spaceLeft === width && word.length >= spaceLeft) {
                        currentLine++;
                        word = word.slice(width);
                    }
                    if (spaceLeft === width) {
                        spaceLeft -= word.length;
                        return;
                    }
                    if (word.length + 1 > spaceLeft) {
                        currentLine++;
                        spaceLeft = width;
                    }
                    spaceLeft -= spaceLeft === width ? word.length : word.length + 1;
                });
                return spaceLeft === width ? currentLine - 1 : currentLine;
            }
            this.content.split('\n').forEach((line) => {
                const wrapTimes = getWrapTimes(line, this.numPerLine) - 1;
                lineNumbers.push(num);
                for(let i = 0; i < wrapTimes; i++) {
                    lineNumbers.push('&nbsp;');
                }
                num++;
            });
            return lineNumbers;
        },
        longestWidth() {
            for (let i = this.lines.length - 1; i >= 0; i--) {
                if (this.lines[i] === '&nbsp;')
                    continue;
                return (this.lines[i] + '').length * this.widthPerChar + 10; // 10px base padding-right
            }
        }
    },
    watch: {
        longestWidth(val, oldVal) {
            if (val !== oldVal) {
                this.$nextTick(() => this.calculateCharactersPerLine())
            }
        }
    },
    methods: {
        calculateCharactersPerLine() { // May be +-1 from real value >_<
            const textarea = this.$refs.textarea;
            const styles = getComputedStyle(textarea);
            const paddingLeft = parseFloat(styles.getPropertyValue('padding-left')); 
            const paddingRight = parseFloat(styles.getPropertyValue('padding-right')); 
            const borderLeft = parseFloat(styles.getPropertyValue('border-left-width')); 
            const borderRight = parseFloat(styles.getPropertyValue('border-right-width')); 
            const fontSize = styles.getPropertyValue('font-size');
            const fontFamily = styles.getPropertyValue('font-family');
            const width = textarea.offsetWidth - paddingLeft - paddingRight - borderLeft - borderRight;
            const helper = this.$refs.helper;
            helper.style.fontSize = fontSize;
            helper.style.fontFamily = fontFamily;
            for (let num = 1; num < 999; num++) {
                helper.innerHTML += 'a';
                if (helper.clientWidth > width - 1) {
                    this.numPerLine = num - 1;
                    break;
                }
            }
            helper.innerHTML = '';
        },
        scrollLines(e) {
            this.scrolledLength = e.target.scrollTop;
            this.syncScroll();
        },
        syncScroll() {
            this.$refs.lines.style.transform = `translateY(${-this.scrolledLength}px)`;
        }
    }
};
</script>

<style scoped>
.lined-textarea {
    display: flex;
    font-size: 13px;
    line-height: 150%;
    font-family: Helvetica, monospace;
}

.lined-textarea__lines {
    background-color: #F0F0F0;
    border: 1px solid #D7E2ED;
    border-radius: 10px 0 0 10px;
    border-right-width: 0;
    padding: 15px 10px 15px 15px;
    overflow: hidden;
    position: relative;
}

.lined-textarea__lines__inner {
    position: absolute;
}

.lined-textarea__lines__line {
    text-align: right;
}

.lined-textarea__lines__line--invalid {
    font-weight: bold;
    color: red;
}
.lined-textarea__content {
    border: 1px solid #D7E2ED;
    border-radius: 0 10px 10px 0;
    border-left-width: 0;
    resize: vertical; /* No simple way to capture resize event(needed for calculate line width), disallow width change */
    margin: 0;
    line-height: inherit;
    font-family: monospace; 
    padding: 15px;
    width: 100%;
    white-space: pre-wrap;
}

.lined-textarea__content:focus {
    outline: none;
}

.count-helper {
    position: absolute;
    visibility: hidden;
}
</style>