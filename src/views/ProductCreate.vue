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
        <!-- <button @click="imageInsert()">이미지만저장</button> -->

        <div class="col-md-9">
          <div class="row">
            <div class="col-auto">                            
              <input type="file" class="form-control" accept="image/png,image/jpeg" @change="registFile($event.target.files, 1)">
            </div>
            <div class="col-auto">
              <input type="file" class="form-control" accept="image/png,image/jpeg" @change="registFile($event.target.files, 2)">
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
      product: {
           itemNm: "", //상품 이름
           price: 0, // 가격
           stockNumber:0, //재고
           itemDetail: "", //상세 설명          
      },
      firstImgFile : "",
      frm : null, // 서버로 보낼 데이터 formData 변수
    };
  },
  computed: {
    user() {
      return this.$store.state.user;
    }
  },
  created() {
    this.frm = new FormData();
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

    async registFile(files, num) { //upload할 file 등록
      let imageFile = files; // 업로드한 파일의 데이터가 여기있음.
      this.imgName = imageFile[0].name;
      console.log("===============11111111========");
      console.log(this.imgName);
      console.log("===========222222222222============");
      console.log("imageFile[0]",imageFile[0]);
      console.log("===========3333333333333333============");

      if (files) {        
        
        if(num == 1) {
           this.frm.append("file1",files[0]);
           this.firstImgFile = "OK";
        } else { 
           this.frm.append("file2",files[0]);
        }
        // this.imageInsert();
      }
    },

    productInsert() {
      if(this.product.itemNm == "") {
        return this.$swal("제품명은 필수 입력값입니다.");
      }

      if(this.product.price == "" || this.product.price == 0) {
        return this.$swal("제품 가격을 입력하세요.");
      }

      if(this.product.stockNumber == "" || this.product.stockNumber == 0) {
        return this.$swal("재고를 입력하세요.");
      }

      if(this.product.itemDetail == "" || this.product.itemDetail == 0) {
        return this.$swal("상세설명을 입력하세요.");
      }

      if(this.firstImgFile == "" || this.firstImgFile == 0) {
        return this.$swal("첫번째 이미지는 필수입니다.");
      }

      this.$swal.fire({
        title: '정말 등록 하시겠습니까?',
        showCancelButton: true,
        confirmButtonText: `제품생성`,
        cancelButtonText: `취소`
      }).then(async (result) => {

        if (result.isConfirmed) {        
          this.uploadItemAndImage();
          this.$swal.fire('저장되었습니다!', '', 'success');
          this.$router.push({path:'/'});
        } 
      });
    },

    async uploadItemAndImage() {
          // console.log("==============> imageInsert()");
          this.frm.append("itemNm",this.product.itemNm);
          this.frm.append("price",this.product.price);
          this.frm.append("stockNumber",this.product.stockNumber);
          this.frm.append("itemDetail",this.product.itemDetail);
           
          await axios.post('/api/productInsert', this.frm, {               
              // param:[this.product]
               headers: {
                      'Content-Type': 'multipart/form-data'
               },
            })
           .then(res => {
              console.log(res.data);                              
           }).catch(error => {
              console.log(error);
           });   
     
         // this.$swal.fire('저장되었습니다요!', '', 'success');
         // this.$router.push({path:'/sales'});
    } 
     
  }
}
</script>