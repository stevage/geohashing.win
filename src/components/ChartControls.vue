<template lang="pug">
#ChartControls
  //-   h3.mb1 Graph
  .group
    label
        input.mr2(type="checkbox" v-model="options.showChart")
        | Show graph
    label.db.mt2(v-if="options.showChart")
        | Graph type
        select.ml2(v-model="options.chartId")
            optgroup(label="Line charts")
                option(v-for="[chartId, settings] of Object.entries(chartSettings).filter(([id, c]) => c.type === 'line')" :value="chartId")
                    | {{ settings.name }}
            optgroup(label="Bar charts")
                option(v-for="[chartId, settings] of Object.entries(chartSettings).filter(([id, c]) => c.type === 'bin')" :value="chartId")
                    | {{ settings.name }}

                //- option(value="weekDay") Week day
            optgroup(label="Scatterplots")
                option(v-for="[chartId, settings] of Object.entries(chartSettings).filter(([id, c]) => c.type === 'scatter')" :value="chartId")
                    | {{ settings.name }}

                //- option(value="latitudeByMonth") Latitude by month
                //- option(value="latitudeByDayOfYear") Latitude by day of year
            optgroup(label="Stacked bar charts")
                option(v-for="[chartId, settings] of Object.entries(chartSettings).filter(([id, c]) => c.type === 'bar')" :value="chartId")
                    | {{ settings.name }}
            optgroup(label="Other")
                option(value="countByWeekday") Count by weekday
                option(value="graticuleByParticipant") Graticule by participant
    label.db.mt2(v-if="options.showChart &&  ['line','bin'].includes(selectedSettings.type) && selectedSettings.x.match(/date|dayOf2008/)")
        | Interval
        select.ml2(v-model="options.interval")
            option(value="day") Day
            option(value="week") Week
            option(value="month") Month
            option(value="year") Year
</template>

<script lang="ts">
import { EventBus } from '@/EventBus'
EventBus.$emit('')

const chartSettings = {
  weekDay: {
    name: 'Week day',
    type: 'bin',
    x: 'date',
    fill: 'weekDayName',
  },
  success: {
    name: 'Success/failure',
    type: 'bin',
    x: 'date',
    fill: 'success',
  },
  participantsCount: {
    name: 'Number of participants',
    type: 'bin',
    x: 'date',
    fill: 'participantsCount',
    scheme: 'turbo',
  },
  participants: {
    name: 'Participants',
    type: 'bin',
    x: 'date',
    fill: 'participantsOrMultiple',
  },
  newbieCount: {
    name: 'Newbies',
    type: 'bin',
    x: 'date',
    fill: true, //'success',
    filter: (e) => e.experienceMin <= 1, // 0 is when there are "no participants"
    scheme: 'turbo',
  },
  experienceMin: {
    name: 'Experience min',
    type: 'bin',
    x: 'date',
    fill: 'experienceMin',
  },
  experienceMax: {
    name: 'Experience max',
    type: 'bin',
    x: 'date',
    fill: 'experienceMax',
  },
  transport: {
    name: 'Transport',
    type: 'bin',
    x: 'date',
    fill: 'transportMode',
  },
  reportKbBin: {
    name: 'Report length',
    type: 'bin',
    x: 'date',
    fill: 'reportKb',
  },
  graticule: {
    name: 'Graticule',
    type: 'bin',
    x: 'date',
    fill: 'graticule',
  },
  graticuleLongitude: {
    name: 'Graticule longitude',
    type: 'bin',
    x: 'date',
    fill: 'graticuleLongitude',
  },
  graticuleLatitude: {
    name: 'Graticule latitude',
    type: 'bin',
    x: 'date',
    fill: 'graticuleLatitude',
  },
  graticuleName: {
    name: 'Graticule name',
    type: 'bin',
    x: 'date',
    fill: 'graticuleNameShort',
  },
  graticuleCountry: {
    name: 'Graticule country',
    type: 'bin',
    x: 'date',
    fill: 'graticuleCountry',
  },
  latitude: {
    name: 'Latitude',
    type: 'scatter',
    x: 'date',
    y: 'latitude',
    fill: 'longitude',
    r: 'participantsCount',
  },
  countByWeekday: {
    name: 'Count by weekday',
    type: 'bin',
    x: 'weekday',
    fill: 'participantsCount',
  },
  countByDayOfYear: {
    name: 'Count by day of year',
    type: 'bin',
    x: 'dayOf2008',
    fill: 'participantsCount',
  },
  reportKb: {
    name: 'Write-up length',
    type: 'bar',
    x: 'yearMonth',
    y: 'reportKb',
    length: 'reportKb',
  },
  participantCountStacked: {
    name: 'Number of participants',
    type: 'bar',
    x: 'yearMonth',
    y: 'participantsCount',
    length: 'participantsCount',
  },
  graticuleByParticipant: {
    name: 'Graticule by participant',
    type: 'cell',
    x: 'participantsOrMultiple',
    y: 'graticuleNameShort',
    fill: 'count',
  },
  medianExperience: {
    name: 'Median experience',
    type: 'line',
    x: 'date',
    y: 'experienceMin',
    transform: 'median',
  },
  averageParticipantCount: {
    name: 'Average participant count',
    type: 'line',
    x: 'date',
    y: 'participantsCount',
    transform: 'mean',
  },
  medianParticipantCount: {
    name: 'Median participant count',
    type: 'line',
    x: 'date',
    y: 'participantsCount',
    transform: 'median',
  },
}

export default {
  name: 'ChartControls',
  data: () => ({
    options: {
      showChart: !!window.location.host.match(/localhost/),
      chartId: 'success',
      interval: 'month',
    },
    chartSettings,
  }),
  created() {
    window.ChartControls = this
  },
  watch: {
    options: {
      deep: true,
      handler() {
        EventBus.$emit('chart-options-change', {
          ...this.options,
          colorBy: this.options.chartId,
        })
      },
    },
  },
  computed: {
    selectedSettings() {
      return chartSettings[this.options.chartId]
    },
  },
}
</script>

<style scoped></style>
