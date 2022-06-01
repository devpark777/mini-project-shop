<template>
  <main class="mt-3">
    <div class="container">
      <div class="row">
        <div class="col-md-5">
          <h5>product_ID:{{productId}}</h5>
          <img :src="`http://localhost:8090/images/item/${repimgUrl}`" class="d-block w-100" alt="..."> 
 
        </div>
        <div class="col-md-7">
          <div class="card shadow-sm">
            <div class="card-body">
              
              <h5 class="card-title">{{productName}}</h5>
              <h5 class="card-title pt-3 pb-3 border-top">{{getCurrencyFormat(price)}}원</h5>
              
              <p class="card-text pb-3">
                배송비 {{getCurrencyFormat(productDetail.delivery_price)}}원 | 도서산간(제주도) 배송비 추가 {{getCurrencyFormat(productDetail.add_delivery_price)}}원 | 택배배송 | {{productDetail.outbound_days}}일 이내 출고 (주말,공휴일 제외)
              </p>
              <div class="card-text border-top pb-3">
                <div class="row">
                  <div class="col-auto">
                    <label class="col-form-label">구매수량</label>
                  </div>
                  <div class="col-auto">
                    <div class="input-group">
                      <span class="input-group-text" style="cursor:pointer;" @click="calculatePrice(-1);">-</span>
                      <input type="text" class="form-control" style="width:40px;" v-model="total">
                      <span class="input-group-text" style="cursor:pointer;" @click="calculatePrice(1);">+</span>
                    </div>
                  </div>
                </div>
              </div>
              <div class="row pt-3 pb-3 border-top">
                <div class="col-6">
                  <h3>총 상품 금액</h3>
                </div>
                <div class="col-6" style="text-align: right;">
                  <h3>{{getCurrencyFormat(totalPrice)}}원</h3>
                </div>
              </div>
              <div class="d-flex justify-content-between align-items-center">
                <div class="col-6 d-grid p-1">
                  <button type="button" class="btn btn-lg btn-dark">장바구니 담기</button>
                </div>
                <div class="col-6 d-grid p-1">
                  <button type="button" class="btn btn-lg btn-danger">주문하기</button>
                </div>
                
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
    
        <div class="col-12" v-for="(one,index) in imgList" :key="index">            
            <img v-if="one != null" :src="`http://localhost:8090/images/item/${one.imgName}`" class="d-block w-100 mt-2" alt="..."> 
        </div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  data() {
    return {
      productId: 0,
      productName: "",
      productDetail: {},
      repimgUrl :"", //대표이미지
      imgList : [],
      total: 1,
      price : 0,
      totalPrice: 0
    };
  },
  created() {
    this.productId = this.$route.query.product_id;
    this.getProductDetail();
  },
  methods: {
    calculatePrice(cnt) {
      let total = this.total + cnt;
      if(total < 1) total = 1;
      this.total = total;
      this.totalPrice = this.price * this.total;
    },
    getCurrencyFormat(value) {
      return this.$currencyFormat(value);
    },
    async getProductDetail() {
    
      let productDetail = await this.$api("/api/productDetail/"+ this.productId);
      
      console.log("서버에서 가저온 상품 상세정보===>", productDetail);
      this.price = productDetail.price;
      this.productName = productDetail.itemNm;
      this.imgList = productDetail.itemImgDtoList; //이미지가 여러장 임
      this.repimgUrl = this.imgList[0].imgName; //이미지중 첫번째 이미지를 대표이미지로 
      this.totalPrice = productDetail.price * this.total;
    },
   
  }
}
</script>