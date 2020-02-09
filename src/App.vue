<template>
    <div id="main-app" class="container">
        <div class="row justify-content-center">
            <add-appointment @add="addItem"/>
            <search-appointment @searchRecords="searchAppointments"
                                :myKey="filterKey"
                                :myDir="filterDir"
                                @requestKey="changeKey"
                                @requestDir="changeDir"/>
            <appointment-list v-bind:appointments="filterApts" @remove="removeItem" @edit="editItem"/>
        </div>
    </div>
</template>

<script>
    import axios from "axios";
    import AppointmentList from "./components/AppointmentList";
    import AddAppointment from "./components/AddAppointment";
    import SearchAppointment from "./components/SearchAppointment";
    import _ from "lodash"

    export default {
        name: 'MainApp',
        data: function () {
            return {
                appointments: [],
                filterKey: "petName",
                filterDir: "asc",
                searchTerms: "",
                aptIndex: 0,
            }
        },

        components: {
            AppointmentList,
            AddAppointment,
            SearchAppointment
        },

        mounted() {
            axios.get("./data/appointments.json")
                .then(response => (this.appointments = response.data.map(item => {
                    item.aptId = this.aptIndex;
                    this.aptIndex++;
                    return item
                })));
        },
        computed: {
            filterApts: function () {
                return _.orderBy(
                    this.searchApts,
                    item => {
                        return item[this.filterKey].toLowerCase();
                    },
                    this.filterDir
                )
            },
            searchApts: function () {
                return this.appointments.filter(item => {
                    return (
                        item.petName.toLowerCase().match(this.searchTerms.toLocaleLowerCase()) ||
                        item.ownerName.toLowerCase().match(this.searchTerms.toLocaleLowerCase()) ||
                        item.aptNotes.toLowerCase().match(this.searchTerms.toLocaleLowerCase())
                    );
                })
            },

        },
        methods: {
            searchAppointments: function (terms) {
                this.searchTerms = terms;

            },
            removeItem: function (apt) {
                this.appointments = _.without(this.appointments, apt);
            },
            editItem: function (id, field, text) {
                const aptIndex = _.findIndex(this.appointments, {
                    aptId: id
                });
                this.appointments[aptIndex][field] = text;
            },
            addItem: function (apt) {
                apt.aptId = this.aptIndex;
                this.aptIndex++;
                this.appointments.push(apt)
            },
            changeKey: function (value) {
                this.filterKey = value;
            },
            changeDir: function (value) {
                this.filterDir = value
            },
        }
    }
</script>

<style>

</style>
