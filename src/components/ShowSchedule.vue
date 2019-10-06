<head>
    <link rel="stylesheet" type="text/css"  href="showschedule.css">
</head>




<template>
    <div id="ShowSchedule">

        <button class="get" v-if='authorized' @click="getData">更新</button>

        <div class="item-container" v-if="authorized && items">
            <ul>

                <li v-for="item in items" v-if="item.start.date">
                    <p>[終日]<b>{{item.summary}}</b></p>
                </li>



                <li v-for="item in items" v-if="item.start.dateTime" >

                    [ {{ isoToTime(item.start.dateTime) }} ~ {{ isoToTime(item.end.dateTime) }} ] <b>{{ item.summary }}</b>

                </li>

                <div class="task">
                    <div class="task_name column is-three-quarters">hanami</div>
                    <div class="column">
                        <p class="time">{{ isoToTime(item.start.dateTime)}}</p>
                        <p class="time">{{ isoToTime(item.end.dateTime) }} </p>
                    </div>
                </div>

            </ul>
        </div>
        <div class='authentification'>

            <button class="sign" v-if='!authorized' @click="handleAuthClick">ログイン</button>
            <button class="sign" v-if='authorized' @click="handleSignoutClick">ログアウト</button>
        </div>
    </div>

</template>



<script>

    // Client ID and API key from the Developer Console
    const CLIENT_ID = '659739173444-lmmpb3tdsrj9f7mi9in6jkb0fk0e0qbc.apps.googleusercontent.com';
    const API_KEY = 'AIzaSyDayzgXjBGRgxG4fgTsrTLIg5mCEkyNCFo';
    // Array of API discovery doc URLs for APIs used by the quickstart
    const DISCOVERY_DOCS = ['https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest'];
    // Authorization scopes required by the API; multiple scopes can be
    // included, separated by spaces.
    const SCOPES = 'https://www.googleapis.com/auth/calendar.readonly';


    export default {
        name: 'ShowSchedule',

        data: function () {
            return {
                api: undefined,
                authorized: false,
                // isoDate: new RegExp('\\d{4}-\\d{2}-\\d{2}T(\\d{2}:\\d{2}):\\d{2}.+', 'g'),
                items: null,
                // timerObj: null,
            }
        },

        created: function () {
            this.api = gapi;
            this.handleClientLoad();
        },

        methods: {

            isoToTime: function(isoExp) {
                let isoRegExp = new RegExp('\\d{4}-\\d{2}-\\d{2}T(\\d{2}:\\d{2}):\\d{2}.+');
                return isoExp.match(isoRegExp)[1]
            },


            handleClientLoad: function () {
                this.api.load('client:auth2', this.initClient);
            },

            initClient: function () {
                let vm = this;

                vm.api.client.init({
                    apiKey: API_KEY,
                    clientId: CLIENT_ID,
                    discoveryDocs: DISCOVERY_DOCS,
                    scope: SCOPES
                })
            },

            handleAuthClick: function (event) {
                Promise.resolve(this.api.auth2.getAuthInstance().signIn())
                    .then(_ => {
                        this.authorized = true;
                    });
            },


            handleSignoutClick: function (event) {
                Promise.resolve(this.api.auth2.getAuthInstance().signOut())
                    .then(_ => {
                        this.authorized = false;
                    });
            },

            getData: function() {
                let vm = this;

                let tMax = new Date();
                let tMin = new Date();


                tMin.setHours(0);
                tMin.setMinutes(0);
                tMin.setSeconds(0);
                tMin.setMilliseconds(0);

                tMax.setHours(0);
                tMax.setMinutes(0);
                tMax.setSeconds(0);
                tMax.setMilliseconds(0);
                tMax.setDate(tMax.getDate() + 1);

                this.api.client.calendar.events.list({
                    'calendarId': 'primary',
                    'timeMax': tMax.toISOString(),
                    'timeMin': tMin.toISOString(),
                    'showDeleted': false,
                    'singleEvents': true,
                    'maxResults': 100,
                    'orderBy': 'startTime'
                }).then(response => {
                    this.items = response.result.items;
                });
            },

        }
    }
</script>

<style scoped>



.task{
    background-color: #fbeefc;
    border-left: 8px solid #c92ad2;
    color: #c92ad2;
    margin: 2em 0;
    padding: 2em;

}

    .task_name{
        font-size: 70px;
    }

    .time{
        font-size: 35px;

    }
    .sign{
        font-size:30px;
        border-radius: 10px;
        margin-top:650px;

    }
    .get{
        font-size:30px;
        float:left;

        margin-top:650px;
        border-radius: 10px;
        margin-right :50px
    }

</style>