<template>
  <main class="mt-3">
    <div class="container">
      <h2 class="text-center">제품 등록</h2>
      <div class="mb-3 row">
        <label class="col-md-3 col-form-label">제품명</label>
        <div class="col-md-9">
          <input type="text" class="form-control" v-model="product.itemNm">
        </div>
      </div>
      <div class="mb-3 row">
        <label class="col-md-3 col-form-label">제품가격</label>
        <div class="col-md-9">
          <div class="input-group mb-3">
            <input type="number" class="form-control" v-model="product.price">
            <span class="input-group-text">원</span>
          </div>
        </div>
      </div>
      <div class="mb-3 row">
        <label class="col-md-3 col-form-label">재고</label>
        <div class="col-md-9">
          <div class="input-group mb-3">
            <input type="number" class="form-control" v-model="product.stockNumber">
            <span class="input-group-text">개</span>
          </div>
        </div>
      </div>
      <div class="mb-3 row">
        <label class="col-md-3 col-form-label">상품상세내용</label>
        <div class="col-md-9">
          <div class="input-group mb-3">
            <textarea class="form-control" v-model="product.itemDetail"></textarea>
          </div>
        </div>
      </div>

      <div class="mb-3 row">
        <label class="col-md-3 col-form-label">상품이미지</label>
        <div class="col-md-9">
          <div class="row">
            <div class="col-auto">
              <img :src="imgName" alt="">
              <input @change="upload" type="file" id="file" class="inputfile" /> 
            </div>
            <div class="col-auto">
              <input @change="upload" type="file" id="file" class="inputfile" /> 
            </div>
          </div>
        </div>
      </div>
     
      <div class="mb-3 row">
        <div class="col-6 d-grid p-1">
          <button type="button" class="btn btn-lg btn-dark" @click="goToList">취소하기</button>
        </div>
        <div class="col-6 d-grid p-1">
          <button type="button" class="btn btn-lg btn-danger" @click="productInsert">저장하기</button>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      // product: {
      //   product_name: "", //상품 이름
      //   product_price: 0, // 가격
      //   count:0, //재고
      //   detail: "", //상세 설명
      //   imgList:[], //이미지 리스트
      // },
        product: {
           itemNm: "", //상품 이름
           price: 0, // 가격
           stockNumber:0, //재고
           itemDetail: "", //상세 설명
           imgList:[], //이미지 리스트
      },
      imgName : null
    
    };
  },
  computed: {
    user() {
      return this.$store.state.user;
    }
  },
  created() {
    this.getCategoryList();
  },
  mounted() {
    if(this.user.email == undefined) {
      alert("로그인을 해야 이용할 수 있습니다.");
      this.$router.push({path:'/'}); 
    }
  },
  methods: {
    goToList(){
      this.$router.push({path:'/sales'}); 
    },
    async getCategoryList(){
      let categoryList = await this.$api("/api/categoryList",{});
      this.categoryList = categoryList;
      
      let oCategory = {};
      categoryList.forEach(item => {
        oCategory[item.category1] = item.id;
      });

      let category1 = [];
      for(let key in oCategory) {
        category1.push(key);
      }

      this.category1 = category1;

      let category2 = [];
      category2 = categoryList.filter(c => {
        return c.category1 == category1[0];
      });

      let oCategory2 = {};
      category2.forEach(item => {
        oCategory2[item.category2] = item.id;
      });

      category2 = [];
      for(let key in oCategory2) {
        category2.push(key);
      }

      this.category2 = category2;

      // console.log(category2);

    },
    changeCategory1(){
      // this.cate1
      this.category3 = [];
      let categoryList = this.categoryList.filter(c => {
        return c.category1 == this.cate1;
      });

      let oCategory2 = {};
      categoryList.forEach(item => {
        oCategory2[item.category2] = item.id;
      });

      let category2 = [];
      for(let key in oCategory2) {
        category2.push(key);
      }

      this.category2 = category2;
    },
    changeCategory2(){
      let categoryList = this.categoryList.filter(c => {
        return (c.category1 == this.cate1 && c.category2 == this.cate2);
      });

      let oCategory3 = {};
      categoryList.forEach(item => {
        oCategory3[item.category3] = item.id;
      });

      let category3 = [];
      for(let key in oCategory3) {
        category3.push(key);
      }

      this.category3 = category3;
    },
     upload(e) {
      let imageFile = e.target.files; // 업로드한 파일의 데이터가 여기있음.
      this.imgName = imageFile.imgName;
      console.log(imageFile[0]);
      let url = URL.createObjectURL(imageFile[0]); // 파일의 필요한 데이터만을 url 변수에 넣음
      console.log(url); // 확인
      this.imageUrl = url; // 미리 작성해둔 imageUrl : ' ' 변수에 가지고있는 경로데이터를 쳐넣음

    },
    productInsert() {
      if(this.product.product_name == "") {
        return this.$swal("제품명은 필수 입력값입니다.");
      }

      if(this.product.product_price == "" || this.product.product_price == 0) {
        return this.$swal("제품 가격을 입력하세요.");
      }

      if(this.product.count == "" || this.product.count == 0) {
        return this.$swal("재고를 입력하세요.");
      }

      if(this.product.detail == "" || this.product.detail == 0) {
        return this.$swal("상세설명을 입력하세요.");
      }

      this.$swal.fire({
        title: '정말 등록 하시겠습니까?',
        showCancelButton: true,
        confirmButtonText: `제품생성`,
        cancelButtonText: `취소`
      }).then(async (result) => {
        if (result.isConfirmed) {
          // await this.$api("/api/productInsert",
          //    {
          //      param:[this.product]
          //    });

          await axios.post('/api/productInsert',{
              // param:[this.product]
              
                itemNm : this.product.itemNm,
                price : this.product.price,
                stockNumber : this.product.stockNumber,
                itemDetail : this.product.itemDetail,
              
            })
           .then(res => {
              console.log(res.data);                              
           }).catch(error => {
              console.log(error);
           });   

          this.$swal.fire('저장되었습니다!', '', 'success');
          this.$router.push({path:'/sales'});
        } 
      });
    }
  }
}
</script>