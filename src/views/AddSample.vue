<template>
  <div>
    <form @submit.prevent="addSample">
      <h2 class="flexify">
        <Icon name="envelope" class="large-icon" />
        {{ this.$store.state.mode == "add" ? "العينة المستلمة" : "تعديل بيانات العينة" }}
      </h2>
      <section class="inputs-container">
        <div class="form-input">
          <label for="receiving_party">جهة الاستلام</label>
          <input type="text" id="receiving_party" name="receiving_party" v-model="sample.receiving_party" required />
        </div>

        <div class="form-input">
          <label for="client_name">اسم العميل</label>
          <select name="client_name" id="client_name" v-model="sample.client_name">
            <option v-for="client in clients" :key="client.id" :value="client.arabic_Name">
              {{ client.arabic_Name }}
            </option>
          </select>
        </div>

        <div class="form-input">
          <label for="date">تاريخ الاستلام</label>
          <input type="text" id="date" name="date" v-model="sample.date" required />
        </div>

        <div class="form-input">
          <label for="description">وصف العينة</label>
          <input type="text" id="description" name="description" v-model="sample.description" required />
        </div>

        <div class="form-input">
          <label for="quantity">عدد العينات</label>
          <input type="text" id="quantity" name="quantity" v-model="sample.qutaity" required />
        </div>

        <div class="form-input">
          <label for="extra">المرفقات</label>
          <input type="text" id="extra" name="extra" v-model="sample.extra" required />
        </div>

        <div class="form-input">
          <label for="status">حالة العينة</label>
          <select name="status" id="status" v-model="sample.status">
            <option value="pending">انتظار التوجيه</option>
            <option value="waiting">انتظار التشغيل</option>
            <option value="in-progress">جاري التصنيع</option>
            <option value="done">منتهية</option>
          </select>
        </div>

        <div class="form-input">
          <label for="price">قيمة العينة</label>
          <input type="text" id="price" name="price" v-model="sample.price" required />
        </div>
      </section>

      <input type="submit" value="حفظ" />
    </form>
  </div>
</template>

<script>
import axios from "axios";
import Icon from "../components/Icon.vue";

export default {
  name: "AddSample",
  components: {
    Icon,
  },
  data() {
    return {
      clients: [],
      sample: {
        receiving_party: "",
        client_name: "",
        date: "",
        description: "",
        qutaity: "",
        extra: "",
        price: "",
        status: "",
      },
    };
  },

  methods: {
    addSample() {
      console.log("addSample");

      if (this.$store.state.mode == "edit")
        axios
          .put("https://backend-apis.herokuapp.com/api/sample/" + this.sample.id, this.sample)
          .then((res) => {
            console.log(res);
            this.$router.push("/samples");
          })
          .catch((err) => {
            console.log(err);
          });
      else
        axios
          .post("https://backend-apis.herokuapp.com/api/sample", this.sample)
          .then((res) => {
            console.log(res);
            this.$router.push("/samples");
          })
          .catch((err) => {
            console.log(err);
          });
    },

    getClients() {
      axios.get("https://backend-apis.herokuapp.com/api/client").then((response) => {
        this.clients = response.data;
      });
    },
  },

  mounted() {
    this.getClients();

    if (this.$store.state.mode == "edit") {
      this.sample = this.$store.state.sample;
    }
  },

  beforeDestroy() {
    this.$store.commit("setMode", "add");
  },
};
</script>
