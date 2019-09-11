<template>
    <p id="ShowDate">
        今日は{{ year }}年{{ month }}月{{ day }}日{{ week }}曜日です。
    </p>

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
            // seconds() {
            //     return zeroPadding(this.date.getSeconds(), 2)
            // },
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

</style>