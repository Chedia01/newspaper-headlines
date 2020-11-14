<template>
<v-app style="background-color:#f7f8f8">
  <v-app-bar
      color="#1b3d4a"
      dark
      max-height="10%"
  > 
      <v-row style="padding-top:4%">
        <v-col
        cols="4"
          sm="4"
          md="3">
      <v-text-field
      label="Search by keyword"
      v-model="keyword"
      ></v-text-field>
      </v-col>
      <v-col
        cols="4"
          sm="4"
          md="3">
     <v-menu
        ref="menu"
        v-model="menu"
        :close-on-content-click="false"
        :return-value.sync="date"
        transition="scale-transition"
        offset-y
        min-width="250px"
      >
        <template v-slot:activator="{ on, attrs }">
          <div v-if="change">
            <v-text-field
              v-model="date"
              label="Search from date"
              prepend-icon="mdi-calendar"
              readonly
              v-bind="attrs"
              v-on="on"
            ></v-text-field>
          </div>
          <div v-else>
            <v-text-field
              v-model="newdate"
              label="Search from date"
              prepend-icon="mdi-calendar"
              readonly
              v-bind="attrs"
              v-on="on"
          ></v-text-field>
          </div>
        </template>
        <v-date-picker
          v-model="date"
          no-title
        >
          <v-spacer></v-spacer>
          <v-btn
            text
            color="#1b3d4a"
            @click="menu = false"
          >
            Cancel
          </v-btn>
          <v-btn
            text
            color="#1b3d4a"
            @click="$refs.menu.save(date),change=true"
          >
            OK
          </v-btn>
        </v-date-picker>
      </v-menu>
      </v-col>
       <v-col
        cols="4"
          sm="4"
          md="3">
      <v-menu
        ref="menu2"
        v-model="menu2"
        :close-on-content-click="false"
        :return-value.sync="date2"
        transition="scale-transition"
        offset-y
        min-width="250px"
      >
        <template v-slot:activator="{ on, attrs }">
          <div v-if="change2">
            <v-text-field
              v-model="date2"
              label="Search to date"
              prepend-icon="mdi-calendar"
              readonly
              v-bind="attrs"
              v-on="on"
            ></v-text-field>
          </div>
          <div v-else>
            <v-text-field
              v-model="newdate"
              label="Search to date"
              prepend-icon="mdi-calendar"
              readonly
              v-bind="attrs"
              v-on="on"
            ></v-text-field>
          </div>
        </template>
        <v-date-picker
          v-model="date2"
          no-title
        >
          <v-spacer></v-spacer>
          <v-btn
            text
            color="#1b3d4a"
            @click="menu2 = false"
          >
            Cancel
          </v-btn>
          <v-btn
            text
            color="#1b3d4a"
            @click="$refs.menu2.save(date2),change2=true"
          >
            OK
          </v-btn>
        </v-date-picker>
      </v-menu>
      </v-col>
      <v-col
        cols="4"
        sm="4"
        md="3">
        <v-btn outlined @click="save(keyword,date,date2)">
         <v-icon left>mdi-magnify</v-icon>
          Search
        </v-btn>
      </v-col>
      </v-row>
    </v-app-bar>
    <v-row style="height: 150px;padding-left:20px">
      <v-col
        v-for="info in information" 
        :key="info.headline"
        cols="3"
        sm="3"
      >
      <v-card
        max-width="300px"
        tile
        outlined
      >
      <v-img
        :src=info.imageUrl
        max-width="300px"
        max-height="130px"
      ></v-img>
      <v-card-title style="font-size:1rem">
        {{ info.headline }}
      </v-card-title>
      <v-card-subtitle>
        <strong>Source:</strong> {{ info.publication }} <br>
        <strong>Date:</strong> {{info.publishDate}} <br>
        <strong>Author:</strong> {{ info.author }}
      </v-card-subtitle>
      <v-card-actions>
        <v-btn color="#1b3d4a" text @click="info.show = !info.show">
        Tell me more
        </v-btn>
      <v-spacer></v-spacer>
      <v-btn icon @click="info.show = !info.show">
        <v-icon>{{ show ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
      </v-btn>
    </v-card-actions>
    <v-expand-transition>
      <v-card
        v-if="info.show"
        class="transition-fast-in-fast-out v-card--reveal"
        style="height:100%"
      >
        <v-card-text
        style="background-color:#1b3d4a;color:white;height:85%">
          {{ info.content }}
          <p><a :href="info.url">{{ info.url }}</a></p>
        </v-card-text>
        <v-card-actions>
          <v-btn
            text
            color="#1b3d4a"
            @click="info.show = false"
          >
            Close
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-expand-transition>
  </v-card>
</v-col>
</v-row>
<div>
  <v-dialog
    width="400px"
    v-model="noresult"
  >
    <v-card style="background-color:#1b3d4a;height:auto">
        <v-card-text style="color:white;padding-top:10px">
          {{ message }}
        </v-card-text>
        <v-divider></v-divider>
        <v-card-actions>
          <v-spacer></v-spacer>
            <v-btn
              outlined
              color="white"
              @click="noresult = false"
            >
            Will do!
            </v-btn>
        </v-card-actions>
    </v-card>
  </v-dialog>
</div>
</v-app>
</template>

<script>
  export default {
    data: () => ({
      noresult: false,
      change:false,
      change2:false,
      show:false,
      menu: false,
      menu2: false,
      message:"",
      newdate:"Select a date",
      keyword:"",
      information:[],
      date: new Date().toISOString().substr(0, 10),
      date2: new Date().toISOString().substr(0, 10),
    }),
    methods:{
      save(keyword,date,date2){
        let apikey = 'a771be289eb7441c95e59ad023a62c3e'
        let params = {}
        if(keyword==""){
          this.noresult = true
          this.message = "Please make sure that you supply a search keyword."
        }
        else if(this.change==false && this.change2==false){
          console.log("2")
          params = {
            q: keyword
          }
        }
        else if(this.change && this.change2==false){
          console.log("3")
          params = {
            q: keyword,
            from: date
          }
        }
        else if(this.change==false && this.change2){
          console.log("4")
          params = {
            q: keyword,
            to: date2
          }
        }
        else{
          console.log("else")
          params = {
            q: keyword,
            from: date,
            to: date2
          }
        }

        this.$http
        .get('https://newsapi.org/v2/everything',{
          params,
          headers: {
            Authorization: 'Bearer ' + apikey
          }
        })
        .then(response => {
          if(response.data.totalResults == 0){
            this.noresult = true
            this.message = "Sorry, we could not find anything that matches your search. Please try a different keyword."
          }
          else{
            var data = response.data.articles
            var dataMap = data.map(value => {
            return {
                headline:value.title,
                author:value.author,
                publication:value.source.name,
                publishDate:value.publishedAt,
                url:value.url,
                imageUrl:value.urlToImage,
                content:value.content,
                show: false
            }
          })
          this.information = dataMap
          }
        })
      },
    }
  }
</script>
<style scoped>
.v-card--reveal {
  bottom: 0;
  opacity: 1 !important;
  position: absolute;
  width: 100%;
}
</style>
