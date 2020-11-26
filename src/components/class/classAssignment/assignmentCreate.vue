<template>
  <v-row style="height: 100%;">
    <v-col cols="8">
      <p class="class-info text-h4">
        과제 등록
      </p>
    </v-col>

    <v-col cols="4" class="d-flex justify-end">
      <v-btn color="accent" @click="$router.go(-1)">
        <v-icon>mdi-arrow-left</v-icon>
      </v-btn>
    </v-col>

    <!-- 강좌 목록 렌더링 영역, 콘텐츠 오버플로우 시 스크롤 적용 -->
    <v-responsive class="overflow-y-auto px-2" height="85%" width="100%">
      <v-card flat>
        <v-card-text>
          <v-form v-model="valid">
            <v-row>
              <v-col class="py-2" cols="12">
                <v-text-field
                  v-model="assignmentTitle"
                  label="과제명"
                  :rules="[rules.required]"
                ></v-text-field>
              </v-col>

              <v-col class="py-2" cols="12">
                <v-textarea
                  v-model="assignmentDescription"
                  label="과제 내용"
                  no-resize
                  :rows="$vuetify.breakpoint.name == 'xs' ? 3 : 6"
                ></v-textarea>
              </v-col>

              <v-col class="py-2" cols="6">
                <v-menu
                  ref="datePicker"
                  v-model="datePicker"
                  :return-value.sync="date"
                  transition="scale-transition"
                  max-width="290px"
                  offset-y
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="date"
                      label="제출 마감일"
                      prepend-icon="mdi-calendar"
                      v-bind="attrs"
                      v-on="on"
                      readonly
                      :rules="[rules.required, rules.checkDueDate]"
                    ></v-text-field>
                  </template>

                  <v-date-picker
                    v-model="date"
                    no-title
                    @change="$refs.datePicker.save(date)"
                  >
                  </v-date-picker>
                </v-menu>
              </v-col>

              <v-col class="py-2" cols="6">
                <v-menu
                  ref="timePicker"
                  v-model="timePicker"
                  :nudge-right="40"
                  :close-on-content-click="false"
                  :return-value.sync="time"
                  transition="scale-transition"
                  max-width="290px"
                  offset-y
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="time"
                      label="제출 마감시간"
                      prepend-icon="mdi-clock-time-four-outline"
                      v-bind="attrs"
                      v-on="on"
                      readonly
                      :rules="[rules.required]"
                    ></v-text-field>
                  </template>

                  <v-time-picker
                    v-model="time"
                    v-if="timePicker"
                    no-title
                    @click:minute="$refs.timePicker.save(time)"
                  ></v-time-picker>
                </v-menu>
              </v-col>

              <v-col class="py-2" cols="12">
                <v-file-input
                  v-model="assignmentFiles"
                  label="첨부 파일"
                  truncate-length="15"
                  show-size
                  multiple
                  counter
                  small-chips
                  :rules="[rules.limitedSize()]"
                >
                </v-file-input>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn outlined :disabled="!valid" @click="createAssignment">
            <v-icon left> mdi-checkbox-marked-circle </v-icon>
            제출하기
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-responsive>
  </v-row>
</template>

<script>
export default {
  data: () => ({
    valid: false,

    timePicker: false,
    datePicker: false,

    // 제출 마감일, 마감시간 초기값 설정
    date: new Date().toISOString().substr(0, 10),
    time: "23:59",

    assignmentTitle: "",
    assignmentDescription: "",
    assignmentFiles: null,

    rules: {
      required: value => !!value || "필수 입력 항목입니다.",
      // 제출 파일 용량 검사
      limitedSize: value =>
        !value || value.size < 50000000 || "첨부 파일 용량 제한은 50MB입니다.",
      checkDueDate: value =>
        new Date(value) > new Date() || "마감일이 오늘보다 이전일 수 없습니다."
    }
  }),

  computed: {
    assignmentDueDate() {
      return this.date + " " + this.time;
    }
  },

  methods: {
    createAssignment() {
      confirm("등록하시겠습니까?");
    }
  }
};
</script>

<style scoped>
.class-info {
  font-weight: 600;
}
.card-text-title {
  font-size: 20px;
  margin-bottom: 0px;
}
</style>