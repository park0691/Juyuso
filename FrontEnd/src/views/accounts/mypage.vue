<template>
  <div class="background">
    <div class="wallet-container mx-auto">
      <div class="wallet">
        <img src="@/assets/wallet_main.png" alt="wallet">

        <div class="profile-card">
          <img src="@/assets/profile.png" alt="profile-card">
          <div class="profile-card-info">
            <v-container>
              <v-row >
                <v-col cols="6">
                  <div v-if="user">
                    <h1 style="font-size: 2rem">주민등록증</h1>
                    <p>별명: {{ user.nickname }}</p>
                    <p>소개: {{ user.description }}</p>
                    <p v-if="user.gender === 'F'">성별: 여</p>
                    <p v-else>성별: 남</p>
                    <p>나이: {{ user.age }}</p>
                  </div>
                </v-col>
                <v-col v-if="user" cols="5">
                  <div class="d-flex justify-content-center" >
                    <v-img max-width="300px" width="100%" height="auto" :src="imgUrl" alt="profile_img"></v-img>
                  </div>
                </v-col>
              </v-row>
            </v-container>
          
          </div>
          <div class="profile-edit-popup my-2">
            <ProfileEditPopup v-if="user"/>

          </div>
          <div class="profile-edit-popup my-6" v-if="!isKakao">
            <PasswordChange :user="user" v-if="user"/>
          </div>
        </div>

        <div class="profile-mydata">
          <div class="profile-mydata-card">
            <img @click="goMyData" src="@/assets/Group 56.png" width="90%" alt="mydata">
          </div>

          <div class="wallet-1">
            <img src="@/assets/wallet_card_1.png" alt="wallet-card">
          </div>

          <div class="profile-calendar">
            <CalenderPopup :user="user" v-if="user"/>
          </div>

            
          <div class="wallet-2">
            <img src="@/assets/wallet_card_2.png" alt="wallet-card">
          </div>


          <div class="friend-block">
            <BlockedFriend/>
          </div>
          
        </div>
        
      
      </div> 
    </div>
  </div>
</template>

<script>
import CalenderPopup from '../../components/accounts/calender-popup.vue'
import ProfileEditPopup from '@/components/accounts/profile-edit-popup.vue'
import BlockedFriend from '@/components/accounts/blocked-friend.vue'
import PasswordChange from '@/components/accounts/password-change.vue'
import { mapGetters } from 'vuex'

export default {
  name: 'MyPage',
  components:{
    CalenderPopup,
    ProfileEditPopup,
    BlockedFriend,
    PasswordChange
  },
  data(){
    return{
      user: null,
      isKakao: false      
    }
  },
  methods: {
    goMyData: function () {
      this.$router.push({name: 'MyData', params: {userId: this.user.id, user:this.user}})
    },
  },
  created(){
    this.user = this.getUser
    if (this.user.provider === 'kakao') {
      this.isKakao = true
    }
  },
  computed: {
    ...mapGetters('accounts', ['getUser']),
    imgUrl: function () {
      if (this.user.imgUrl) {
        return `${process.env.VUE_APP_IMG_URL}/${this.user.imgUrl}`
      } else {
        return require('@/assets/basic_profile.png')
      }
    }
  }
}
</script>

<style scoped>

.background {
  background-size : cover;
  height: 100vh;
  background-image: linear-gradient( rgba(0, 0, 0, 0.3), rgba(0, 0, 0, 0.5) ), url(https://i.pinimg.com/originals/b5/2b/5b/b52b5b29d6acc1c13cc0eb41db6a56c4.jpg);
}

.wallet-container {
  height: 90vh;
  display:flex;
  justify-content:center;
  align-items:center;

}

img {
 max-width: 100%;
}

.wallet {
  position: relative;
}

.profile-card {
  width: 41%;
  position: absolute;
  top: 18%;
  left: 5%;
  transform: rotate(6deg);
}

.profile-card:hover {
  transform: scale(1.3);
}

.profile-card-info {
  width: 90%;
  position: absolute;
  top: 5%;
  left: 5%;
  font-size: 1.5rem;
}

.profile-mydata {
  width: 40%;
  position: absolute;
  top: 18%;
  right: 2.5%;
}


.profile-mydata-card {
  cursor: pointer;
}

.profile-mydata-card:hover {
  transform: translateY(-30%);
  cursor: pointer;
}

.profile-calendar {
  position:absolute;
  top: 50%;
  width: 90%;
  cursor: pointer;
}

.profile-calendar:hover {
  transform: translateY(-30%);
}

.wallet-1 {
  position: absolute;
  top: 35%;
  right: 0.5%;
  width: 110%;
  
}

.wallet-2 {
  position: absolute;
  top: 85%;
  width: 96%;
  
}

.friend-block {
  position: absolute;
  top: 140%;
  right: 13%;
}

.profile-edit-popup {
  width: 100px;
}

</style>
