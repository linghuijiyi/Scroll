<template>
    <div>
        <div class='header' @click='headleBanner'>
            <img src="../assets/banner.png"/>
        </div>
        <div class='list-wrapper' ref='wrapper'>
            <div>
                <div class='top-tip'>
                    <span v-text='pull'></span>
                </div>
                <ul class='list-content'>
                    <li class='list-item' v-for='(item, index) in newsList' @click='headleClick(item.url, item.title)' :key='index'>
                        <div class='avatar'>
                            <img :src="item.imgUrl"  />
                        </div>
                        <div class='text'>
                            <div class='title' v-text='item.title'></div>
                            <div class='desc' v-text='item.desc'></div>
                            <div class='applyNum'>申请人数&nbsp;:&nbsp;<span style='color: red;'>{{item.time}}</span>人</div>
                        </div>
                    </li>
                </ul>
                <div class='bottom-tip'>
                    <span v-text='pullUp'></span>
                </div>
            </div>
        </div>
        <div class='alert' ref='alert' v-text='alert'></div>
    </div>
</template>

<script>
    import BScroll from 'better-scroll'
    import axios from 'axios'
    export default {
        name: 'Scroll',
        props: {
            probeType: {
                type: Number,
                default: 1
            },
            click: {
                type: Boolean,
                default: true
            },
            pullup: {
                type: Boolean,
                default: true
            },
            listenScroll: {
                type: Boolean,
                default: true
            }
        },
        data () {
            return {
                newsList: [
                    {
                        imgUrl: require('../assets/jijie.jpg'),
                        title: '集借',
                        desc: '一分钟申请，最高额度十万，当天放款',
                        time: '397451',
                        url: 'http://jjhao.geotmtai.com/ix.html?c=47'
                    },
                    {
                        imgUrl: require('../assets/beibei.jpg'),
                        title: '呗呗',
                        desc: '海量出借人，等你来借钱',
                        time: '668546',
                        url: 'https://lxbh5.51luocheng.com/?channel_id=6'
                    },
                    {
                        imgUrl: require('../assets/suyingqianbao.png'),
                        title: '速盈钱包',
                        desc: '3分钟申请 最高100万',
                        time: '541271',
                        url: 'http://syqb.zhongmengzn.com/static/download.html?show=1&agentid=1241842'
                    }, 
                    {
                        imgUrl: require('../assets/rongshu.png'),
                        title: '榕树',
                        desc: '高通过 额度高',
                        time: '414785',
                        url: 'https://www.rongshu.cn/promotion/m/c/index.html?c=dk36530'
                    }, 
                    {
                        imgUrl: require('../assets/wwh.png'),
                        title: '旺旺花',
                        desc: '高通过 额度高',
                        time: '414785',
                        url: 'http://www.qqyzk.top/dxjk_reg.ssj?merchantId=955'
                    }, 
                    {
                        imgUrl: require('../assets/mlgj.png'),
                        title: '木兰管家',
                        desc: '高通过 额度高',
                        time: '414785',
                        url: 'https://xmulan.caihang.com/static/register_guide.html?inviteCode=XZSC7'
                    }
                ],
                pull: '下拉刷新',
                alert: '刷新成功',
                pullUp: '上拉查看更多',
                flag: false
            }
        },
        mounted () {
            setTimeout(() => {
                this._initScroll();
            }, 20);
        },
        methods: {
            _initScroll () {
                this.scroll = new BScroll(this.$refs.wrapper, {
                    probeType: this.probeType,
                    click: this.click,
                    scrollbar: {
                        fade: false,
                    }
                });
                if (this.listenScroll) {
                    this.scroll.on('scroll', (pos) => {
                        if (pos.y > 30) {
                            this.pull = '释放立即刷新';
                        }
                    });
                }
                if (this.pullup) {
                    this.scroll.on('touchEnd', (pos) => {
                        if (pos.y > 30) {
                            setTimeout(() => {
                                this.pull = '下拉刷新';
                                this.refreshAleat('刷新成功');
                                this.scroll.refresh();
                            }, 1000);
                        } else if (pos.y < (this.scroll.maxScrollY - 30)) {
                            this.pullUp = '加载中...';
                            setTimeout(() => {
                                this.pullUp = '上拉查看更多';
                                if (this.flag) {
                                    this.flag = false;
                                    this.scroll.refresh();
                                    this.newsList.push({
                                        imgUrl: require('../assets/qianzhan.png'),
                                        title: '钱站',
                                        desc: '芝麻分600+  2分钟下款',
                                        time: '541245',
                                        url: 'https://m.iqianzhan.com/outsideRegister.html?qd=jieqianyong7'
                                    });
                                } else {
                                    this.pullUp = '没有更多数据了...';
                                    this.scroll.refresh();
                                }
                            }, 2000);
                        }
                    });
                }
            },
            headleClick (url, name) {
                this.statistics(name, url);
            },
            headleBanner () {
                const url = 'https://resource.cmbchina.com/itafront/TrafficAcquisitionFront/cobrandcard/index.html?#/login/wzrycmb/remote/N3700MM20857557700CY';
                this.statistics('信用卡', url);
            },
            statistics (name, url) {
                const api = 'https://mimiguan.mimidai.com/mimidai-api/basic/loanMarsVisitor';
                if (this.getCookie('username') === undefined || this.getCookie('username') === null) this.setCookie('username', 'mimiguan' + this.uuid());
                axios.get(api, { 
                    params: {
                        name: name,
                        uniqueId: this.getCookie('username')
                    }
                }).then((res) => {
                    window.location.href = url;
                })
            },
            refreshAleat (text) {
                text = text || '操作成功';
                this.alert = text;
                this.$refs.alert.style.display = 'block';
                setTimeout(() => {
                    this.$refs.alert.style.display = 'none';
                }, 1500);
            },
            getCookie (key) {
                let all = document.cookie;
                let retvalue = null;
                if (all) {
                    let kvs = all.split('; ');
                    kvs.forEach((item) => {
                        let kv = item.split('=');
                        if (key === kv[0]) {
                            retvalue = kv[1];
                            return false;
                        }
                    });
                    return retvalue;
                }
            },
            setCookie (name, value) {
                let exp = new Date();
                exp.setTime(exp.getTime() + 24 * 60 * 60 * 1000 * 7);
                document.cookie = name + "=" + escape(value) + ";expires=" + exp.toGMTString();
            },
            random () {
                return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
            },
            uuid () {
                return (this.random() + this.random() + "-" + this.random() + "-" + this.random() + this.random() + this.random());
            }
        }
    }
</script>

<style scoped lang='stylus'>
    .header
        position fixed
        z-index 2
        left 0
        top 0
        height 1.8rem
        line-height 1.8rem
        text-align center
        color #fff
        font-size 20px
        font-family 'SimHei'
        letter-spacing 5px
        width 100%
        background-color #35a251
        img
            display block
            width 100%
            height 100%
    .list-wrapper
        width 100%
        position fixed
        z-index 1
        top 1.8rem
        bottom 0px
        left 0
        background-color #ccc
        overflow hidden
        .list-content
            background-color #fff
            .list-item
                display flex
                padding 10px
                background-color #eee
                border-bottom 10px solid #fff
                .avatar
                    flex 0 0 1.6rem
                    width 1.6rem
                    img
                        width 100%
                        border-radius 15px
                        display block
                .text
                    padding 0 10px
                    .title
                        font-size .35rem
                        width 100%
                        line-height .6rem
                        color rgba(7, 17, 27, 1)
                    .desc
                        font-size .28rem
                        line-height .5rem
                        color rgba(7, 17, 27, 1)
                        overflow hidden
                        text-overflow ellipsis
                        white-space nowrap
                    .applyNum
                        line-height .5rem
                        font-size .28rem
                        color rgba(7, 17, 27, 0.7)
            // .list-item:last-child
            //     border-bottom 1px solid #ccc
        .top-tip
            position absolute
            top -40px
            left 0
            z-index 1
            width 100%
            height 40px
            line-height 40px
            text-align center
            color #555
        .bottom-tip
            width 100%
            height 35px
            line-height 35px
            color #777
            background-color #f2f2f2
            text-align center
            color #555
    .alert
        display none
        position fixed
        top 1.8rem
        left 0
        z-index 2
        width 100%
        height 35px
        line-height 35px
        text-align center
        color #fff
        font-size 15px
        background-color rgba(7, 17, 27, .7)
</style>
