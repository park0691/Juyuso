<template>
  <v-menu
    v-model="menuShow"
    offset-y
    :close-on-content-click="false">
      <template v-slot:activator="{ on: menu, attrs }">
        <v-btn
          class="search-btn d-flex justify-space-between"
          color="white"
          v-bind="attrs"
          v-on="{ ...menu }"
          rounded
        >
          <span class="ml-1">검색</span>
          <v-icon class="m-0" color="grey">mdi-magnify</v-icon>
        </v-btn>
      </template>
      <v-card width="300">
        <v-card-title>
          <v-text-field
            @input="roomSearch(tab)"
            v-model="searchInput"
            placeholder="검색어를 입력해주세요"
          ></v-text-field>
        </v-card-title>
        <v-tabs
          fixed-tabs
          v-model="tab"
          slider-color="#1B1B32">
          <v-tab
            @click="roomSearch(tab)"
            v-for="item in items"
            :key="item.value"
          >{{ item.name }}
          </v-tab>
        </v-tabs>
        <v-tabs-items
          class="search-result"
          style="overflow-y: scroll"
          v-model="tab">
          <v-tab-item
            v-for="item in items"
            :key="item.tab"
          >
            <v-card flat>
              <button style="text-align: left">
              <v-card-text
                @click="openDetailPopup(result)"
                  v-for="result in searchResult[items[tab].value]"
                  :key="result.meetingId">
                {{ result.meetingTitle }}
                <span v-if="tab==1">
                  <v-chip
                    :key="hashtag"
                    v-for="hashtag in filterHastag(result.hashtag)">
                  {{ hashtag }}  
                  </v-chip>
                </span>
              </v-card-text>
              </button>
            </v-card>
          </v-tab-item>
        </v-tabs-items>
      </v-card>
      <TableDetailPopup
        ref="detailpopup"
        :search="true" 
        :roomInfo="propsRoomInfo"/>

    </v-menu>
</template>

<script>
import api from '@/common/api'
import TableDetailPopup from '@/components/table_list/table-detail-popup.vue'

export default {
  name: 'TableSearch',
  components: {
    TableDetailPopup
  },
  data: function () {
    return {
      searchInput: "",
      items: [
        {name: '방 제목', value: 'title',},
        {name: '해시태그', value: 'tags'},
      ],
      tab: 0,
      searchResult: {
        'title': [],
        'tags': [],
      },
      menuShow: false,
      propsRoomInfo: null,
    }
  },
  watch: {
    menuShow: function (val) {
      if (!val) {
        this.searchInput = ""
        this.searchResult.title = []
        this.searchResult.tags = []
      }
    }
  },
  methods: {
    roomSearch: function (tab) {
      if (this.searchInput.trim()) {
        api.get('/meeting/search', {
          params :{
            [this.items[tab].value] : this.searchInput 
          }
        }).then(res => this.searchResult[this.items[tab].value] = res.data.content)
      }
      else {
        this.searchResult['title'] = []
        this.searchResult['tags'] = []
      }
    },
    openDetailPopup: function (roomInfo) {
      this.propsRoomInfo = roomInfo
      this.$refs.detailpopup.dialog = true
    },
    filterHastag: function (hashtags) {
      let results = hashtags.filter( hashtag => hashtag.indexOf(this.searchInput) != -1)
      return results
    }
  }
}
</script>

<style scoped>
  .search-btn {
    width: 250px
  }

  .search-result {
    height: 300px
  }

</style>
