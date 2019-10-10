<template>
    <div class="ShowDate">
        <p><span class="is-centered">{{ year }}</span></p>
    <p class="date"><span class="large">{{ month }}</span>月<span class="large">{{ day }}</span>日</p>

        <p><span class="container">{{ week }}曜日</span></p>
        <p><span class="mid3 ">{{hour}}:{{min}}</span></p>
    </div>
</template>

<script>

    const zeroPadding = (num, digit) => {
        return (Array(digit).join("0") + num).slice(-digit)
    };

    export default {
        name: "ShowDate",

        data(){
            return {
                date: new Date(),
            }
        },

        computed: {
            year() {
                return this.date.getFullYear()
            },
            month() {
                return zeroPadding(this.date.getMonth() + 1, 2)
            },
            week() {
                const WeekChars = [ "日", "月", "火", "水", "木", "金", "土" ];
                return WeekChars[this.date.getDay()]
            },
            day() {
                return zeroPadding(this.date.getDate(), 2)
            },
            hour() {
                return zeroPadding(this.date.getHours(), 2)
            },
            min(){
                return zeroPadding(this.date.getMinutes(), 2)
            }
        },

        mounted() {
            this.setDate();
            setInterval(() => this.setDate(), 1000);
        },
        methods: {
            setDate() {
                this.date = new Date()
            }
        }

    }
</script>

<style scoped>
p{
    font-size: 45px;
    font-family:OCRB;
    text-align: center;
    vertical-align: middle;

}

.large {
    font-size: 150px;
    text-shadow: 0 0 2px white;
}

.mid1{


}
.mid2{



}

.mid3 {
    font-size: 80px;

}
.ShowDate{

}

</style>