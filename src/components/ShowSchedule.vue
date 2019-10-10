<template>
    <div id="ShowSchedule">
        <div class='authentification'>
            <button class="sign" v-if='!authorized' @click="handleAuthClick">ログイン</button>
            <button class="sign" v-if='authorized' @click="handleSignoutClick">ログアウト</button>
            <button class="get" v-if='authorized' @click="getData">更新</button>
            <a href="https://calendar.google.com/calendar/r" target="_blank">
                <button class="google" v-if='authorized' >予定の追加</button>
            </a>

        </div>

        <div class="item-container" v-if="authorized && items">
            <div v-for="item in items" v-if="item.start.date">
                <div class="task_name a_task">
                    {{ item.summary }}
                </div>
            </div>

            <div v-for="item in items" v-if="item.start.dateTime">
                <div class="a_task columns">
                    <div class="task_name column is-three-quarters">
                         <input type="checkbox" class="checkbox" value="1" >
                        {{ item.summary }}
                    </div>
                    <div class="time column">
                        <p>{{ isoToTime(item.start.dateTime) }}<span class="time2"></span></p>
                        <p>{{ isoToTime(item.end.dateTime) }}<span class="time2"></span></p>
                    </div>
                </div>
            </div>
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
                timerObj: null,
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
                        this.authorized = true
                    });
                this.getData();
                this.timerObj = setInterval(this.getData, 5000)
            },
            handleSignoutClick: function (event) {
                Promise.resolve(this.api.auth2.getAuthInstance().signOut())
                    .then(_ => {
                        this.authorized = false;
                        clearInterval(this.timerObj)
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
                    'maxResults': 4,
                    'orderBy': 'startTime'
                }).then(response => {
                    this.items = response.result.items;
                });
            },
        }
    }
</script>

<style scoped>
    .a_task{
        margin:50px;
        color: #232323;
        background-color:lightblue;
        border-left: solid 10px cornflowerblue;
        border-radius: 10px;
        height: 150px;
        width:500px
    }


    .task_name{
        font-size: 40px;
        vertical-align: middle;
    }
    .time{
        font-size: 40px;
    }
    .item-container{
        margin:10px;
    }
    .sign{
        display: inline-block;
        padding: 0.5em 1em;
        text-decoration: none;

        transition: .4s;
        margin:3px;
        background: #668ad8;
        color: #FFF;
        border-radius: 4px;
        box-shadow: 0px 0px 0px 5px #668ad8;
        border: dashed 1px #FFF;
        margin-right:20px;

    }
    .get{
        display: inline-block;
        padding: 0.5em 1em;
        text-decoration: none;
        transition: .4s;
        margin:3px;
        background: #668ad8;
        color: #FFF;
        border-radius: 4px;
        box-shadow: 0px 0px 0px 5px #668ad8;
        border: dashed 1px #FFF;
        margin-right:20px;

    }
    .google{
        display: inline-block;
        padding: 0.5em 1em;
        text-decoration: none;

        transition: .4s;
        margin:3px;
        background: #668ad8;
        color: #FFF;
        border-radius: 4px;
        box-shadow: 0px 0px 0px 5px #668ad8;
        border: dashed 1px #FFF;
        margin-right:20px;
    }
    .checkbox {
        display: table-cell;
        width:       24px;
        height:          24px;
        -moz-transform:       scale(1.4);
        -webkit-transform: scale(1.4);
        transform:    scale(1.4);
        margin-right:10px;

    }
    .time2{
        font-size:10px;
    }
</style>