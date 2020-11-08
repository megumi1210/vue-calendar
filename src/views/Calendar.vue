<template>

    <div class="app">
        <div class="dialog" v-show="showDialog">
            <label>
                添加新的日程
                <input type="text">
            </label>

            <button class="confirm_btn" @click="showDialog=false">确认</button>
        </div>

        <div :class="showDialog?'cal_container showDialog':'cal_container'">


            <div class="cal_header">

                <div class="cal_header_date">
                    <button @click="preMonth"><</button>
                    <span class="cal_year_month">{{displayTitle}}</span>
                    <button @click="nextMonth">></button>
                </div>
                <button class="today_shortcut" @click="changeToNow">今天</button>

            </div>

            <div class="cal_body">
                <div class="cal_body_title">
                    <div class="cal_title_weekday">
                        <span>星期日</span>
                    </div>

                    <div class="cal_title_weekday">
                        <span>星期一</span>
                    </div>

                    <div class="cal_title_weekday">
                        <span>星期二</span>
                    </div>

                    <div class="cal_title_weekday">
                        <span>星期三</span>
                    </div>

                    <div class="cal_title_weekday">
                        <span>星期四</span>
                    </div>

                    <div class="cal_title_weekday">
                        <span>星期五</span>
                    </div>

                    <div class="cal_title_weekday">
                        <span>星期六</span>
                    </div>

                </div>

                <div class="cal_body_content">
                    <div class="cal_row" v-for=" i in rows">
                        <div class="cal_col" :class="days[(i-1)*7+j-1].isChosen?'chosen':'normal'" v-for="j in 7"
                             @click="changeChosen(i,j)">
                            <div class="cal_body_day">
                                <span :class="days[(i-1)*7+j-1].isToday?'today':'normal'">{{days[(i-1)*7+j-1].day}}</span>
                            </div>

                            <div class="cal_body_schedule">
                                <button v-if="days[(i-1)*7+j-1].isChosen" v-on:click.stop="showDialogView(i,j)">添加日程标题</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
    export default {
        name: "Calendar",
        data() {
            return {
                date: {},
                year: '',
                month: '',
                day: '',
                days: [],
                rows: 0,
                nowMonth: '',
                showDialog: false
            }
        },
        created() {
            this.initNowDate()
        },
        computed: {
            displayTitle() {
                if (this.month / 10 === 0) {
                    return this.year + ' 年  ' + this.month + ' 月'
                }
                return this.year + ' 年 ' + this.month + ' 月'
            }
        },
        methods: {
            initDateParams() {
                this.year = this.date.getFullYear();
                this.month = this.date.getMonth() + 1;
                this.day = this.date.getDate();
                this.initDays()
            },
            initDays() {
                this.days = [];
                let firstDay = new Date(this.year, this.month - 1, 1);
                let lastDay = new Date(this.year, this.month, 0);
                let firstWeek = firstDay.getDay();
                let lastWeek = lastDay.getDay();

                //需要补全的上个月的天数
                let lastMonthDays = firstWeek;
                let now = new Date();
                now.setDate(0);
                //获取上个月的最后一天
                let preMonthDay = now.getDate();
                //上个月的日子补全
                for (let i = lastMonthDays - 1; i >= 0; i--) {
                    let data = {};
                    data.day = preMonthDay - i;
                    data.isToday = false;
                    data.isChosen = false;
                    this.days.push(data)

                }
                //当前月份的补齐
                for (let i = firstDay.getDate(); i <= lastDay.getDate(); i++) {
                    let data = {};
                    data.day = i;
                    data.isToday = this.day === i && this.month === this.nowMonth;
                    data.isChosen = false;
                    this.days.push(data)
                }
                //获取下一个月的第一天
                let nextMonthDay = new Date(this.year, this.month, 1).getDate();
                //下个月份的补齐
                for (let i = 0; i < 6 - lastWeek; i++) {
                    let data = {};
                    data.day = nextMonthDay + i;
                    data.isChosen = false;
                    this.days.push(data)
                }

                this.rows = this.days.length / 7;

            },
            initNowDate() {
                let now = new Date();
                this.date = now;
                this.nowMonth = now.getMonth() + 1;
                this.initDateParams()
            },
            changeToNow() {
                this.initNowDate()
            },
            changeDate(date) {
                this.date = date;
                this.initDateParams()
            },
            nextMonth() {
                let date = new Date(this.year, this.month, this.day);
                this.changeDate(date)
            },
            preMonth() {
                let date = new Date(this.year, this.month - 2, this.day);
                this.changeDate(date)
            },
            changeChosen(i, j) {

                this.days[(i - 1) * 7 + j - 1].isChosen = !this.days[(i - 1) * 7 + j - 1].isChosen;

            },
            showDialogView(i,j){
                console.log("子元素点击了");
                this.showDialog =true
            }
        }

    }
</script>

<style lang="less" scoped>

    .showDialog {
        opacity: 40%;
    }

    .app {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;

        .dialog {
            width: 300px;
            height: 300px;
            border-radius: 8px;
            position: absolute;
            z-index: 2;
            margin: 10px auto;
            background: whitesmoke;
            box-shadow: 2px 2px grey;
        }


    }


    .cal_container {
        width: 800px;


        .cal_header {
            display: flex;
            justify-content: center;
            border: 1px solid black;
            position: relative;
            padding-top: 20px;
            padding-bottom: 20px;

            .cal_year_month {
                margin-left: 10px;
                margin-right: 10px;
            }

            .today_shortcut {
                position: absolute;
                right: 10px;
            }
        }


        .cal_body {
            border: 1px solid black;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;


            .cal_body_title {

                display: flex;
                justify-content: space-between;
                width: 712px;
                margin-top: 20px;
                margin-bottom: 5px;


                .cal_title_weekday {
                    width: 100px;
                    text-align: left;
                }

            }


            .cal_body_content {
                margin-bottom: 20px;
                display: flex;
                flex-direction: column;
                justify-content: center;

                .cal_row {
                    display: inline-flex;
                }

                .chosen {
                    opacity: 80%;
                    background: linear-gradient(to bottom, #fbed96, #abecd6);
                }

                .cal_col {
                    border: 1px solid grey;
                    width: 100px;
                    height: 100px;

                    &:hover {
                        opacity: 60%;
                    }


                    .cal_body_day {
                        padding-top: 5px;
                        padding-right: 5px;
                        text-align: right;

                        .normal {

                        }

                        .today {
                            padding-left: 3px;
                            padding-right: 3px;
                            border-radius: 20px;
                            background: #007AFF;
                        }


                    }

                    .cal_body_schedule {
                        display: flex;
                        justify-content: center;
                        margin-top: 10px;
                    }

                }


            }
        }
    }

</style>