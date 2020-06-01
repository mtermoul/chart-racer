<template>
    <div class="indigo--text">
        <h1>This is the home page...</h1>
        <v-btn color="primary" @click="loadData2">Load Data</v-btn>
        <div>{{ processMessage }}</div>
    </div>
</template>

<script>
// import sParse from 'csv-parse/lib/sync';
// import parse from 'csv-parse';
import papa from 'papaparse';
import moment from 'moment';

export default {
    name: 'Home',
    data() {
        return {
            byDay: {},
            byDaySum: {},
            processMessage: ''
        };
    },
    methods: {
        // async loadData1() {
        //     this.processMessage = 'Loading data...';
        //     console.log('Loading data...');
        //     // const inputFile = 'data/latestdata.csv';
        //     const inputFile = 'data/part1.csv';

        //     const res = await fetch(inputFile);
        //     if (res) {
        //         const data = await res.text();
        //         this.processMessage = '----- data: ' + data.length;
        //         console.log(this.processMessage);
        //         // ----- using callbacks ---------
        //         // parse(data, (err, recs) => {
        //         //     console.log('----- Records', recs.length);
        //         // });
        //         // using on events
        //         let parser = parse(data, { from_line: 1, to_line: 5 });
        //         parser.on('readable', () => {
        //             let record;
        //             while ((record = parser.read())) {
        //                 console.log('--- record: ', record);
        //             }
        //         });
        //         parser.on('error', error => {
        //             console.log('--- parser error', error);
        //         });
        //         parser.on('end', () => {
        //             console.log('---- End of parsing...');
        //         });
        //         // let records = parse(data);
        //         // this.processMessage = '----- records: ' + records.length;
        //         // console.log(this.processMessage);
        //         // records.forEach(row => {
        //         //     console.log('---- Row', row[4], row[5]);
        //         // });
        //     }
        // },
        loadData2() {
            console.log('---- Starting the papaparse');
            // 'ID,age,sex,city,province,country,latitude,longitude,geo_resolution,date_onset_symptoms,date_admission_hospital,date_confirmation,symptoms,lives_in_Wuhan,travel_history_dates,travel_history_location,reported_market_exposure,additional_information,chronic_disease_binary,chronic_disease,source,sequence_available,outcome,date_death_or_discharge,notes_for_discussion,location,admin3,admin2,admin1,country_new,admin_id,data_moderator_initials,travel_history_binary';
            const inputFile = `${window.location.origin}/data/latestdata.csv`;
            // const inputFile = `${window.location.origin}/data/part1.csv`;
            let sum = {};
            papa.parse(inputFile, {
                header: true,
                worker: true,
                download: true,
                preview: 100,
                step: row => {
                    console.log('-- step', row.data);
                    const eventDate = moment(
                        row.data.date_confirmation,
                        'DD.MM.YYYY'
                    ).format('MM/DD/YYYY');
                    if (eventDate) {
                        if (!sum.hasOwnProperty(eventDate)) {
                            sum[eventDate] = {};
                        }
                        if (!sum[eventDate].hasOwnProperty(row.data.country)) {
                            // sum[eventDate][row.data.country] = { count: 0, countToDate: 0 };
                        }
                        sum[eventDate][row.data.country].count += 1;
                    }
                },
                complete: (res, file) => {
                    console.log('--- parsing complete', res, file);
                }
            });
            this.byDay = sum;
        }
    }
};
</script>
