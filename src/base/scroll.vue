<template>
    <div ref='wrapper'>
        <slot></slot>
    </div>
</template>

<script>
    import BScroll from 'better-scroll';
    export default {
        name: 'scrll',
        props: {
            probeType: {
                type: Number,
                default: 1
            },
            click: {
                type: Boolean,
                default: true
            },
            data: {
                type: Array,
                default: null
            },
            pullup: {
                type: Boolean,
                default: false
            },
            pulldown: {
                type: Boolean,
                defautl: false
            },
            listenScroll: {
                type: Boolean,
                default: true
            },  
            refreshDelay: {
                type: Number,
                default: 20
            }
        },
        mounted () {
            setTimeout(() => {
                this._initScroll();
            }, 20);
        },
        methods: {
            _initScroll () {
                if (!this.$refs.wrapper) return;
                this.scroll = new BScroll(this.$refs.wrapper, {
                    probeType: this.probeType,
                    click: this.click
                });
                if (this.listenScroll) {
                    this.scroll.on('scroll', (pos) => {
                        if (pos.y > 30) {
                            this.$emit('scroll');
                        }
                    });
                }
                if (this.pullup || this.pulldown) {
                    this.scroll.on('touchEnd', (pos) => {
                        if (pos.y > 30) {
                            this.$emit('pullup');
                        } else if(pos.y < (this.scroll.maxScrollY - 30)) {
                            this.$emit('pulldown');
                        }
                    });
                }
            },
            refresh () {
                this.scroll && this.scroll.refresh();
            }
        },
        watch: {
            data () {
                setTimeout(() => {
                    this.refresh();
                }, this.refreshDelay);
            }
        }
    }
</script>