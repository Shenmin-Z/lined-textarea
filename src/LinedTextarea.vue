<template>
    <div class="lined-textarea">
        <div class="lined-textarea__lines"
             :style="{ 'padding-right': longestWidth}"
        >
            <div class="lined-textarea__lines__inner"
                 ref="lines"
            >
               <p v-for="(line, index) in lines"
                :key="index"
                class="lined-textarea__lines__line"
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
    </div>
</template>

<script>
export default {
    name: 'LinedTextarea',
    mounted() {
        this.syncScroll();
    },
    data() {
        return {
            content: '',
            widthPerChar: 8, // Hard coded
            scrolledLength: 0
        };
    },
    props: {
        validation: String,
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
        lines() {
            return this.content.split('\n').map((line, index) => index + 1);
        },
        longestWidth() {
            for (let i = this.lines.length - 1; i >= 0; i--) {
                if (this.lines[i] === '&nbsp;')
                    continue;
                return (this.lines[i] + '').length * this.widthPerChar + 10 + 'px'; // 10: base padding-right
            }
        }
    },
    methods: {
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

.lined-textarea > * {
    border: 1px solid #D7E2ED !important;
    box-shadow: none !important;
}

.lined-textarea__lines {
    background-color: #F0F0F0 !important;
    border-radius: 10px 0 0 10px;
    border-right-width: 0 !important;
    padding: 15px 10px 15px 15px;
    overflow: hidden;
    position: relative;
    flex-shrink: 1;
}

.lined-textarea__lines__inner {
    position: absolute;
}

.lined-textarea__lines__line {
    text-align: right;
}

.lined-textarea__content {
    flex-shrink: 100;
    border-radius: 0 10px 10px 0;
    border-left-width: 0 !important;
    resize: vertical;
    margin: 0;
    line-height: inherit;
    font-family: monospace !important; 
    padding: 15px;
    width: 100%;
    white-space: pre;
    overflow: scroll;
    overflow-wrap: normal;
}

.lined-textarea__content:focus {
    outline: none;
}
</style>