<template>
    <div v-click-outside>
        <input type="text" :value="formatDate">
        <div class="pannel" v-if="isVisible">
            <div class="pannel-nav">
                <span @click="prewYear">&lt;</span>
                <span @click="prewMonth">&lt;&lt;</span>
                <span>{{time.year}}年</span>
                <span>{{time.month+1}}月</span>
                <span @click="nextMonth">&gt;&gt;</span>
                <span @click="nextYear">&gt;</span>
            </div>
            <div class="pannel-content">
                <div class="days">
                <span v-for="j in 7" :key="`_`+j" class="cell">
                {{weekDays[j-1]}}</span>
                <div v-for="i in 6" :key="i">
                    <span v-for="j in 7" :key="j" class="cell cell-days" @click="chooseDate(visibleDays[(i-1)*7+(j-1)])"
                    :class="[{notCurrentMonth:!isCurrentMonth(visibleDays[(i-1)*7+(j-1)])},
                            {today:isToday(visibleDays[(i-1)*7+(j-1)])},
                            {select:isSelect(visibleDays[(i-1)*7+(j-1)])}]"
                            >
                        {{visibleDays[(i-1)*7+(j-1)].getDate()}}
                    </span>

                </div>
                </div>
            </div>
            <div class="pannel-footer">
                今天
            </div>
        </div>
    </div>
</template>
<script>
import * as utils from './util'
export default {
    directives:{
        clickOutside:{
            bind(el,bindings,vnode){
                let handler = (e)=>{
                    if(el.contains(e.target)){
                        if(!vnode.context.isVisible){
                        vnode.context.focus()
                        }
                    } else{
                        if(vnode.context.isVisible){
                            vnode.context.blur()
                        }
                        
                    }
                }
                el.handler = handler
                document.addEventListener('click',handler)
            },
            unbind(el){
                document.removeEventListener('click',el.handler)
            }
        }
    },
    data(){
        let {year,month} = utils.getYearMontDay(this.value)
        return {
            weekDays:['日','一','二','三','四','五','六'],
            time:{year,month},
            isVisible:false
        }
    },
    props:{
        value:{
            type:Date,
            default:()=>new Date()
        }
    },
    methods: {
        focus(){
            this.isVisible = true;
        },
        blur(){
            this.isVisible = false;
        },
        isCurrentMonth(date){
            let {year,month} = utils.getYearMontDay(utils.getDate(this.time.year,this.time.month,1));
            let{year:y,month:m} = utils.getYearMontDay(date);
            return year === y && month === m;
        },
        // 是否是今天
        isToday(date){
            let {year,month,day} = utils.getYearMontDay(new Date());
            let{year:y,month:m,day:d} = utils.getYearMontDay(date);
            return year === y && month === m && day === d;
        },
        // 点击日期
        chooseDate(date){
            this.time = utils.getYearMontDay(date)
            this.$emit('input',date);
            this.blur();//关闭弹层
        },
        isSelect(date){
            // 获取当前年月日
            let {year,month,day} = utils.getYearMontDay(this.value);
            let{year:y,month:m,day:d} = utils.getYearMontDay(date);
            return year === y && month === m && day === d;
        },
        prewMonth(){
            let d = utils.getDate(this.time.year,this.time.month,1);
            d.setMonth(d.getMonth()-1);
            this.time = utils.getYearMontDay(d);
        },
        nextMonth(){
            let d = utils.getDate(this.time.year,this.time.month,1);
            d.setMonth(d.getMonth()+1);
            this.time = utils.getYearMontDay(d);
        },
        prewYear(){
            let d = utils.getDate(this.time.year,this.time.month,1);
            d.setYear(d.getFullYear()-1);
            this.time = utils.getYearMontDay(d);
            
        },
        nextYear(){
            let d = utils.getDate(this.time.year,this.time.month,1);
            d.setYear(d.getFullYear()+1);
            this.time = utils.getYearMontDay(d);
        }
    },
    mounted(){
        
    },
    computed:{
        visibleDays(){
           let {year,month} = utils.getYearMontDay(utils.getDate(this.time.year,this.time.month,1))
           let currentFirstDay = utils.getDate(year,month,1)
           let week = currentFirstDay.getDay()
           let starDay = currentFirstDay - week * 60 * 60 * 1000 * 24 
            let arr = [];
            for(let i=0;i<42;i++){
                arr.push(new Date(starDay + i*60*60*1000*24))
            }
            return arr
        },
        formatDate(){
            let {year,month,day} = utils.getYearMontDay(this.value)
            return `${year}-${month+1}-${day}`
        }
    },
}
</script>
<style>
    .pannel{
        position: absolute;
        background: white;
        box-shadow: 2px 2px 2px pink,-2px -2px 2px pink;
    }
    .pannel-nav{
        height: 30px;
        display: flex;
        justify-content: space-around;
    }
    .pannel-nav span{
        cursor: pointer;
        user-select: none;
    }
    .pannel-content .days .cell{
        height: 32px;
        width: 32px; 
        display: inline-flex;
        justify-content: center;
        align-items: center;
        font-weight: bold;
        box-sizing: border-box;
    }
    .pannel-content .days .cell-days:hover,.select{
        border: 1px solid pink;
    }
    .pannel-footer{
        height: 30px;
        text-align: center;
    }
    .notCurrentMonth{
        color: rgb(218, 214, 214);
    }
    .today{
        background: red;
        color: white;
        border-radius: 4px
    }
</style>