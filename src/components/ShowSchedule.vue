<template>
    <div id="ShowSchedule">
        <div class='authentification'>
            <h2>Simple Calendar</h2>

            <button v-if='!authorized' @click="handleAuthClick">Sign In</button>
            <button v-if='authorized' @click="handleSignoutClick">Sign Out</button>
        </div>
        <hr>
        <button v-if='authorized' @click="getData">Get Data</button>
        <div class="item-container" v-if="authorized && items">
            <h3>本日の予定</h3>

            <ul>
                <li v-for="item in items" v-if="item.start.date">
                    [終日]<b>{{item.summary}}</b>
                </li>

                <li v-for="item in items" v-if="item.start.dateTime">

                    [ {{ isoToTime(item.start.dateTime) }} ~ {{ isoToTime(item.end.dateTime) }} ] <b>{{ item.summary }}</b>

                </li>
            </ul>
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

</style>