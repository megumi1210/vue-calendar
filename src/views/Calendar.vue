<template>

    <div class="app">
        <div class="dialog" v-show="showDialog">


            <div class="dialog_title">
                <span>{{getDialogTitle}}</span>
            </div>


            <div class="dialog_content">
                <div class="form_group">
                    <input type="text" placeholder="请输入日程标题" v-model="title" v-on:keyup="inputCheck">
                     <span v-if="showErrorMsg" class="errorMsg">请输入标题</span>
                </div>

                <div>
                    <span>{{current_date}}</span>
                </div>
            </div>


            <div class="dialog_footer">
                <button class="delete_btn" @click="deleteSchedule()">删除</button>
                <button class="confirm_btn" @click="confirmSchedule()">确认</button>
            </div>

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
                                <button v-if="!days[(i-1)*7+j-1].hasTitle && days[(i-1)*7+j-1].isChosen" v-on:click.stop="showDialogView(i,j)" class="add_schedule_btn">
                                    {{days[(i-1)*7+j-1].title}}
                                </button>

                                <span v-if="days[(i-1)*7+j-1].hasTitle" class="schedule_span" v-on:click.stop="showDialogView(i,j)">{{days[(i-1)*7+j-1].title}}</span>
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
                showDialog: false,
                current_date: '',
                current_i:'',
                current_j:'',
                current_title:'',
                title:'',
                schedule: new Map() ,
                isChosen:new Map(),
                showErrorMsg:false
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
            },
            getDialogTitle(){

                if(this.current_i !=='' && this.current_j !==''){
                    let data = this.days[(this.current_i - 1) * 7 + this.current_j - 1];
                    if(data.hasTitle){
                        return '编辑日程标题';
                    }else {
                        return '添加日程标题'
                    }
                }else {
                    return ''
                }



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
                    data.day = preMonthDay - i -1;
                    data.month = this.month -1;
                    data.year = this.year;
                    data.isToday = false;
                    this.fillData(data);
                    this.days.push(data)

                }
                //当前月份的补齐
                for (let i = firstDay.getDate(); i <= lastDay.getDate(); i++) {
                    let data = {};
                    data.day = i;
                    data.month = this.month;
                    data.year = this.year;
                    data.isToday = this.day === i && this.month === this.nowMonth;
                    this.fillData(data);
                    this.days.push(data)
                }
                //获取下一个月的第一天
                let nextMonthDay = new Date(this.year, this.month, 1).getDate();
                //下个月份的补齐
                for (let i = 0; i < 6 - lastWeek; i++) {
                    let data = {};
                    data.day = nextMonthDay + i;
                    data.month = this.month + 1;
                    data.year = this.year;
                    this.fillData(data);
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
                let item = this.days[(i - 1) * 7 + j - 1];
                let key = item.year + "-" + item.month + "-" + item.day;
                if(item.isChosen){
                    this.isChosen.delete(key)
                }else {
                    this.isChosen.set(key,true)
                }
                item.isChosen = !item.isChosen;

            },
            showDialogView(i, j) {
                this.showDialog = true;
                let item = this.days[(i - 1) * 7 + j - 1];
                this.current_date = item.year + '-' + item.month + '-' + item.day;
                this.current_i = i;
                this.current_j = j;
                if(item.hasTitle){
                    this.title = item.title;
                }else {
                    this.title ='';
                }

            },
            deleteSchedule() {
                this.showDialog = false;
                let data = this.days[(this.current_i - 1) * 7 + this.current_j - 1];
                let key = data.year + "-" + data.month + "-" + data.day;
                data.title='添加日程标题';
                data.hasTitle=false;
                this.schedule.delete(key)

            },
            confirmSchedule(){
                if(this.title !== ''){
                    this.showDialog = false;
                    let data = this.days[(this.current_i - 1) * 7 + this.current_j - 1];
                    let key = data.year + "-" + data.month + "-" + data.day;
                    this.schedule.set(key,{hasTitle:true,title:this.title});
                    console.log(JSON.stringify(this.schedule.get(key)));
                    data.title=this.schedule.get(key).title;
                    data.hasTitle=true;
                    this.title=''
                }else {
                    this.showErrorMsg =true
                }
            },
            fillData(data) {
                let key = data.year + "-" + data.month + "-" + data.day;
                if (this.schedule.has(key)) {
                    data.hasTitle = true;
                    data.title = this.schedule.get(key).title;
                } else {
                    data.hasTitle = false;
                    data.title = '添加日程标题';
                }

                if(this.isChosen.has(key)){
                    data.isChosen = this.isChosen.get(key)
                }else {
                    data.isChosen = false;
                }

            },
            inputCheck(){
                    this.showErrorMsg =this.title===''
            }

        }

    }
</script>

<style lang="less" scoped>

    .showDialog {
        opacity: 40%;
    }

    .schedule_span{
        color: grey;
        background: sandybrown;
        width: 80px;
        text-align: center;
    }

    .add_schedule_btn{
        background: #FCF4F2;
        color: grey;
        outline: none;


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
            background: white;
            box-shadow: 2px 2px grey;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;

            .dialog_title {
                margin-top: 20px;

                span {
                    font-size: 18px;
                    width: 300px;
                }

            }

            .dialog_content {
                display: flex;
                flex-direction: column;
                justify-content: space-between;
                height: 100px;
                width: 80%;
                margin-bottom: 20px;

                input {
                    width: 100%;
                    padding-left: 10px;
                    outline: none;
                    border: 0;
                    border-bottom: 1px solid grey;
                }

                .errorMsg{
                    font-size: 10px;
                    color:red;
                    float: right;
                    margin-top: 5px;
                }
            }

            .dialog_footer {
                margin-bottom: 20px;

                .confirm_btn {
                    color: #fff;
                    background: #007AFF;
                    border-radius: 5px;
                    outline: none;
                    border: 0;
                    height: 30px;
                    width: 50px;
                    margin-left: 40px;

                    &:hover {
                        opacity: 80%;
                        cursor: pointer;
                    }
                }

                .delete_btn {
                    color: #fff;
                    background: crimson;
                    border-radius: 5px;
                    outline: none;
                    border: 0;
                    height: 30px;
                    width: 50px;

                    &:hover {
                        opacity: 80%;
                        cursor: pointer;
                    }
                }
            }
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