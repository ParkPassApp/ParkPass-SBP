<template>
  <div>
    <div class="list">
      <p class="list__title">Выберите банк</p>
      <div class="list__inner">
        <a
          class="list__list-item list-item"
          v-for="(item, index) in banks"
          :key="index"
          :title="item.bankName"
          :href="getBankLink(item.schema, item.package_name)"
        >
          <img
            class="list-item__logo"
            :src="item.logoURL"
            :alt="item.bankName"
          />
          <span class="list-item__name">{{ item.bankName }}</span>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "SbpList",
  // Пример ссылки, генерируемой платежным агрегатором -
  // https://qr.nspk.ru/AD10006LB9KMLJJ582RB4QCOBJJ8QISL?type=02&bank=100000000004&sum=12900&cur=RUB&crc=7DDB
  props: ["qrLink"],
  data() {
    return {
      banks: [],
      iOS:
        (!window.MSStream && /iPad|iPhone|iPod/.test(navigator.userAgent)) ||
        /^((?!chrome|android).)*safari/i.test(navigator.userAgent),
    };
  },
  created() {
    this.getBanks();
  },
  methods: {
    getBanks() { // Получаем список банков, через openapi сбп
      axios.get("https://qr.nspk.ru/proxyapp/c2bmembers.json").then(res => {
        this.banks = res.data.dictionary;
      });
    },
    getBankLink(schema, package_name) { // Генерируем ссылку для каждого банка, в зависимости от устройства android\ios
      return this.iOS
        ? `${this.qrLink.replace("https:", schema + ":")}`
        : `${this.qrLink.replace(
            "https:",
            "intent:"
          )}#Intent;scheme=${schema};package=${package_name};end;`;
    },
  }
};
</script>
