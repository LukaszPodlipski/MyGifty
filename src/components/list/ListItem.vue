<template>
  <div class="gift">
    <img v-if="imgSrcExists" :src="imgUrl" alt="giftimage" />
    <img v-else src="../../assets/default_gift_image.svg" alt="giftimage" />

    <div class="content">
      <h3>{{ name }}</h3>
      <div class="line"></div>
      <div>
        <p>{{ price }} zł</p>
        <div>
          <base-button-small link :href="url">Zobacz</base-button-small>
          <base-button-small mode="black">Zarezerwuj</base-button-small>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: ["id", "name", "price", "url", "imgUrl"],
  data() {
    return {
      imgSrcExists: true,
    };
  },
  methods: {
    checkIfImageExists(url, callback) {
      const img = new Image();

      img.src = url;

      if (img.complete) {
        callback(true);
      } else {
        img.onload = () => {
          callback(true);
        };

        img.onerror = () => {
          callback(false);
        };
      }
    },
  },
  created() {
    this.checkIfImageExists(this.imgUrl, exists => {
      if (exists) {
        this.imgSrcExists = true;
      } else {
        this.imgSrcExists = false;
      }
    });
  },
};
</script>

<style lang="scss" scoped>
.gift {
  display: flex;
  flex-direction: row;
  background-color: #303134;
  border-radius: 15px;
  margin: 40px 0 20px 0;
  color: #fefefe;
  img {
    border-radius: 15px 0px 0 15px;
    max-height: 180px;
    width: 180px;
    object-fit: cover;
    background-color: #fefefe;
  }
  .content {
    width: 100%;
    padding: 1.5rem 2rem 1rem 2rem;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-self: center;
    h3 {
      font-size: 1.5rem;
      font-weight: 600;
    }
    div {
      display: flex;
      flex-direction: row;
      align-items: flex-end;
      justify-content: space-between;
      p {
        font-size: 1.8rem;
        color: #9aa0a6;
        margin: 0 0 0.5rem 0.5rem;
      }
    }
    .line {
      width: 100%;
      height: 0;
      border: 1px solid #9aa0a698;
      margin: 1rem 0;
    }
  }
}
</style>