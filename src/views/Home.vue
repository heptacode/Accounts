<template>
  <v-app>
    <v-container>
      <v-layout align-center justify-center style="height: 100vh">
        <v-layout class="mt-4" align-center justify-center>
          <v-flex xs11 sm10 md8>
            <v-layout class="mt-4" justify-end>
              <v-btn light href="/login">비밀번호 변경</v-btn>
            </v-layout>
            <v-form v-model="form" onsubmit="return false">
              <v-layout align-center justify-center class="mb-4">
                <v-img
                  src="../assets/logo-white.png"
                  lazy-src="../assets/logo-white.png"
                  max-width="60"
                  max-height="60"
                ></v-img>
              </v-layout>
              <v-text-field
                v-model="name"
                label="성명"
                color="#00B8D4"
                :rules="[rules.required]"
                autofocus
                required
              ></v-text-field>
              <v-text-field
                v-model="id"
                label="사용자 ID"
                color="#00B8D4"
                :rules="[rules.required]"
                hint="Wi-Fi 네트워크 로그인에 사용되는 ID 입니다."
                required
              ></v-text-field>
              <v-text-field
                v-model="password"
                label="비밀번호"
                color="#00B8D4"
                :rules="[rules.required]"
                hint="타 사이트와 동일한 비밀번호를 사용하지 마십시오."
                required
              ></v-text-field>
              <v-text-field
                v-model="phone"
                label="전화번호"
                color="#00B8D4"
                :rules="[rules.required]"
                hint="하이픈을 입력하지 마십시오."
                maxlength="11"
                type="tel"
                required
              ></v-text-field>
              <v-text-field
                v-model="email"
                label="이메일"
                color="#00B8D4"
                :rules="[rules.required]"
                type="email"
                required
              ></v-text-field>
              <v-btn
                text
                block
                class="mt-5"
                color="#00B8D4"
                @click="(loader = 'loading'), submit()"
                :disabled="loading || !form"
                :loading="loading"
                >제출
                <v-icon class="ml-3">mdi-send</v-icon>
              </v-btn>
            </v-form>
          </v-flex>
        </v-layout>
      </v-layout>
    </v-container>
    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="headline mr-2">저장됨</span>
          <v-icon>check</v-icon>
        </v-card-title>
        <v-card-text>
          <v-layout align-center justify-center>
            사용자 등록을 요청하였습니다.<br />
            등록이 승인되면 SMS로 알려드리겠습니다.
          </v-layout>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn light @click="dialog = false" onclick="location.replace('/')"
            >승인</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-app>
</template>

<script>
const hashStr =
  "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
export default {
  data() {
    return {
      dialog: false,
      loader: null,
      loading: false,
      form: false,
      hash: "",
      name: "",
      id: "",
      password: "",
      phone: "",
      email: "",
      rules: {
        required: value => !!value || "필수 사항입니다."
      }
    };
  },
  watch: {
    loader() {
      const l = this.loader;
      this[l] = !this[l];
      setTimeout(() => (this[l] = false), 5000);
      this.loader = null;
    }
  },

  methods: {
    submit() {
      this.hash = "";
      for (let i = 0; i < 4; i++) {
        this.hash += hashStr.charAt(Math.floor(Math.random() * hashStr.length));
      }
      // eslint-disable-next-line
      firebase
        .firestore()
        .collection("accounts")
        .doc(this.name + "_" + this.hash)
        .set({
          // eslint-disable-next-line
          created: firebase.firestore.FieldValue.serverTimestamp(),
          name: this.name,
          id: this.id,
          password: this.password,
          phone: this.phone,
          email: this.email
        })
        .then(() => {
          this.dialog = true;
          this.name = "";
          this.id = "";
          this.password = "";
          this.phone = "";
          this.email = "";
        })
        .catch(() => {
          alert("오류가 발생했습니다. 다시 시도하십시오.");
        });
    }
  }
};
</script>