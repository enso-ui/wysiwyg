<template>
    <div :class="['wysiwyg-wrapper', {'has-error': hasError}, $attrs.class]">
        <component :is="editorComponent"
            :key="componentKey"
            v-bind="editorBindings"/>
    </div>
</template>

<script>
import { defineAsyncComponent } from 'vue';
import Editor from '@tinymce/tinymce-vue';

export default {
    name: 'Wysiwyg',

    inheritAttrs: false,

    data: () => ({
        editorKey: 0,
        observer: null,
        mediaQuery: null,
        theme: 'light',
    }),

    props: {
        hasError: {
            type: Boolean,
            required: true,
        },
        editor: {
            type: String,
            default: 'tinymce',
            validator: value => ['tinymce', 'trix'].includes(value),
        },
        menubar: {
            type: [String, Boolean],
            default: false,
        },
        plugins: {
            type: String,
            default: 'codesample autolink link autoresize lists emoticons image preview table',
        },
        toolbar: {
            type: String,
            default: 'newdocument undo redo bold italic strikethrough underline codesample h2 bullist numlist alignleft aligncenter alignright alignjustify blockquote indent outdent link table emoticons forecolor backcolor preview removeformat',
        },
    },

    computed: {
        editorComponent() {
            return this.editor === 'trix'
                ? defineAsyncComponent(() => import('./TrixEditor.vue'))
                : Editor;
        },
        componentKey() {
            return this.editor === 'tinymce'
                ? this.editorKey
                : this.editor;
        },
        editorBindings() {
            if (this.editor === 'trix') {
                return Object.fromEntries(Object.entries(this.$attrs)
                    .filter(([key]) => key !== 'apiKey'));
            }

            return {
                ...this.$attrs,
                toolbar: this.toolbar,
                plugins: this.plugins,
                init: this.editorInit,
            };
        },
        isDarkTheme() {
            return this.theme === 'dark';
        },
        themeTokens() {
            if (typeof window === 'undefined') {
                return {
                    bodyBg: this.isDarkTheme ? '#263245' : '#ffffff',
                    bodyText: this.isDarkTheme ? '#e5edf8' : '#0f172a',
                    link: this.isDarkTheme ? '#7cc0ff' : '#2563eb',
                    border: this.isDarkTheme ? '#5b6f8f' : '#d8dee6',
                    blockquote: this.isDarkTheme ? '#c9d7ea' : '#475569',
                };
            }

            const styles = getComputedStyle(document.documentElement);

            return {
                bodyBg: styles.getPropertyValue('--bulma-scheme-main').trim()
                    || (this.isDarkTheme ? '#263245' : '#ffffff'),
                bodyText: styles.getPropertyValue('--bulma-text').trim()
                    || (this.isDarkTheme ? '#e5edf8' : '#0f172a'),
                link: styles.getPropertyValue('--bulma-link').trim()
                    || (this.isDarkTheme ? '#7cc0ff' : '#2563eb'),
                border: styles.getPropertyValue('--bulma-border').trim()
                    || '#d8dee6',
                blockquote: styles.getPropertyValue('--bulma-text-light').trim()
                    || (this.isDarkTheme ? '#c9d7ea' : '#475569'),
            };
        },
        editorInit() {
            return {
                menubar: this.menubar,
                skin: false,
                content_css: false,
                content_style: this.contentStyle,
            };
        },
        contentStyle() {
            const {
                bodyBg, bodyText, link, border, blockquote,
            } = this.themeTokens;

            return `
                html { background: ${bodyBg}; }
                body {
                    background: ${bodyBg};
                    color: ${bodyText};
                    padding: 0.75rem;
                }
                a { color: ${link}; }
                blockquote {
                    border-left: 3px solid ${border};
                    color: ${blockquote};
                    margin-left: 0;
                    padding-left: 1rem;
                }
                table td, table th {
                    border: 1px solid ${border};
                }
            `;
        },
    },

    mounted() {
        this.updateTheme();
        this.watchTheme();
    },

    beforeUnmount() {
        this.observer?.disconnect();
        this.mediaQuery?.removeEventListener?.('change', this.handleThemeMutation);
    },

    methods: {
        resolveTheme() {
            const root = document.documentElement;
            const explicitTheme = root.dataset.theme;

            if (explicitTheme === 'dark' || explicitTheme === 'light') {
                return explicitTheme;
            }

            return window.matchMedia('(prefers-color-scheme: dark)').matches
                ? 'dark'
                : 'light';
        },
        updateTheme() {
            const nextTheme = this.resolveTheme();

            if (nextTheme !== this.theme) {
                this.theme = nextTheme;
                this.editorKey += 1;
            }
        },
        handleThemeMutation() {
            this.updateTheme();
        },
        watchTheme() {
            this.observer = new MutationObserver(this.handleThemeMutation);
            this.observer.observe(document.documentElement, {
                attributes: true,
                attributeFilter: ['data-theme', 'class'],
            });

            this.mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
            this.mediaQuery.addEventListener?.('change', this.handleThemeMutation);
        },
    },
};
</script>

<style lang="scss">
.wysiwyg-wrapper {
    .tox.tox-tinymce {
        background-color: var(--bulma-scheme-main);
        border: 1px solid var(--bulma-border);
        border-radius: var(--bulma-radius);
        box-shadow: none;
    }

    .tox-editor-header,
    .tox-toolbar-overlord,
    .tox-toolbar,
    .tox-toolbar__primary,
    .tox-menubar,
    .tox-statusbar {
        background-color: var(--bulma-scheme-main-ter) !important;
        border-color: var(--bulma-border) !important;
        color: var(--bulma-text) !important;
    }

    .tox .tox-tbtn,
    .tox .tox-tbtn svg,
    .tox .tox-statusbar__wordcount,
    .tox .tox-statusbar__path-item,
    .tox .tox-statusbar__branding a,
    .tox .tox-statusbar__text-container {
        color: var(--bulma-text) !important;
        fill: var(--bulma-text) !important;
    }

    .tox .tox-tbtn:hover,
    .tox .tox-tbtn:focus,
    .tox .tox-tbtn--enabled,
    .tox .tox-tbtn--enabled:hover {
        background-color: var(--bulma-scheme-main-bis) !important;
        color: var(--bulma-text-strong) !important;
    }

    .tox .tox-tbtn:hover svg,
    .tox .tox-tbtn:focus svg,
    .tox .tox-tbtn--enabled svg,
    .tox .tox-tbtn--enabled:hover svg {
        fill: var(--bulma-text-strong) !important;
    }

    .tox .tox-edit-area::before {
        border-color: var(--bulma-input-hover-border-color) !important;
    }

    .tox .tox-edit-area iframe {
        background-color: transparent !important;
    }

    .tox .tox-statusbar {
        border-top: 1px solid var(--bulma-border) !important;
    }
}

.wysiwyg-wrapper.has-error {
    border: 1px solid var(--bulma-danger);
}

.wysiwyg-wrapper {
    trix-toolbar {
        .trix-button-row {
            flex-wrap: wrap;
        }

        .trix-button-group {
            background-color: var(--bulma-scheme-main-ter);
            border-color: var(--bulma-border);
        }

        .trix-button {
            border-color: var(--bulma-border);
        }

        .trix-button:not(:disabled) {
            color: var(--bulma-text);
        }

        .trix-button.trix-active {
            background-color: var(--bulma-scheme-main-bis);
            color: var(--bulma-text-strong);
        }
    }

    trix-editor {
        background-color: var(--bulma-scheme-main);
        border-color: var(--bulma-border);
        border-radius: var(--bulma-radius);
        color: var(--bulma-text);
        min-height: 10rem;

        &:focus {
            border-color: var(--bulma-input-focus-border-color);
            box-shadow: var(--bulma-input-focus-box-shadow-size)
                var(--bulma-input-focus-box-shadow-color);
        }
    }

    &.has-error {
        trix-editor {
            border-color: var(--bulma-danger);
        }
    }
}
</style>
