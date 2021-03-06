<template>
  <v-card class="wrapper" :loading="loading">
    <v-card-title>開催時間</v-card-title>
    <v-card-text>
      <v-container>
        <v-form ref="form" :value="isOk" lazy-validation>
          <v-row>
            <v-col cols="12" class="subtitle-1 black--text py-0"
              >開始日時</v-col
            >
          </v-row>
          <v-row class="ml-4">
            <v-col cols="6" class="pt-0 pb-1">
              <v-menu
                v-model="startDateOpen"
                :close-on-content-click="false"
                :nudge-right="40"
                transition="scale-transition"
                offset-y
                min-width="290px"
              >
                <template v-slot:activator="{ on }">
                  <v-text-field
                    v-model="startDate"
                    :rules="[rules.required]"
                    readonly
                    label="日付"
                    v-on="on"
                  />
                </template>
                <v-date-picker v-model="startDate" locale="ja" />
              </v-menu>
            </v-col>
            <v-col cols="6" class="pt-0 pb-1">
              <v-menu
                v-model="startTimeOpen"
                :close-on-content-click="false"
                :nudge-right="40"
                readonly
                transition="scale-transition"
                offset-y
                min-width="290px"
              >
                <template v-slot:activator="{ on }">
                  <v-text-field
                    v-model="startTime"
                    :disabled="!startDate"
                    :rules="[rules.required]"
                    label="時刻"
                    v-on="on"
                  />
                </template>
                <v-time-picker v-model="startTime" format="24hr" locale="ja" />
              </v-menu>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" class="subtitle-1 black--text py-0"
              >終了日時</v-col
            >
          </v-row>
          <v-row class="ml-4">
            <v-col cols="12" sm="6" class="py-1">
              <v-menu
                v-model="endDateOpen"
                :close-on-content-click="false"
                :nudge-right="40"
                readonly
                transition="scale-transition"
                offset-y
                min-width="290px"
              >
                <template v-slot:activator="{ on }">
                  <v-text-field
                    v-model="endDate"
                    :disabled="!startDate || !startTime"
                    :rules="[rules.required]"
                    label="日付"
                    v-on="on"
                  />
                </template>
                <v-date-picker v-model="endDate" locale="ja" />
              </v-menu>
            </v-col>
            <v-col cols="12" sm="6" class="py-1">
              <v-menu
                v-model="endTimeOpen"
                :close-on-content-click="false"
                :nudge-right="40"
                transition="scale-transition"
                offset-y
                min-width="290px"
              >
                <template v-slot:activator="{ on }">
                  <v-text-field
                    v-model="endTime"
                    :rules="[rules.required]"
                    :disabled="!startDate || !startTime || !endDate"
                    readonly
                    label="時刻"
                    v-on="on"
                  />
                </template>
                <v-time-picker v-model="endTime" format="24hr" locale="ja" />
              </v-menu>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" class="py-0">
              <span v-if="!isOk" class="red--text text--darken1">
                時刻の前後関係が一致していません
              </span>
            </v-col>
          </v-row>
        </v-form>
      </v-container>
    </v-card-text>
  </v-card>
</template>

<script lang="ts">
import { Vue, Component, Watch, Emit, Prop } from 'nuxt-property-decorator'
@Component
export default class TimeCard extends Vue {
  startDateOpen = false
  startTimeOpen = false
  endDateOpen = false
  endTimeOpen = false

  @Prop()
  loading?: boolean

  startDate: string | null = null
  startTime: string | null = null
  endDate: string | null = null
  endTime: string | null = null

  rules = {
    required: (text: string | Date | null) =>
      !!text || 'このフィールドは必須です'
  }

  get isOk() {
    if (!this.startDate || !this.startTime || !this.endDate || !this.endTime)
      return true
    const start = Date.parse(`${this.startDate} ${this.startTime}`)
    const end = Date.parse(`${this.endDate} ${this.endTime}`)
    return start <= end
  }

  @Watch('startDate')
  onChangeStartDate() {
    this.change()
  }

  @Watch('startTime')
  onChangeStartTime() {
    this.change()
  }

  @Watch('endDate')
  onChangeEndDate() {
    this.change()
  }

  @Watch('endTime')
  onChangeEndTime() {
    this.change()
  }

  @Emit()
  submit() {}

  change() {
    this.$emit(
      'change',
      this.startDate,
      this.startTime,
      this.endDate,
      this.endTime,
      this.startDate &&
        this.startTime &&
        this.endDate &&
        this.endTime &&
        this.isOk
    )
  }
}
</script>

<style scoped>
.wrapper {
}
</style>
