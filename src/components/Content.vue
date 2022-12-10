<template>
  <v-container>
    <v-row class="text-center text-center">
      <v-col cols="12">
        <div class="text-center d-flex justify-center">
        <v-card dense class="d-flex px-5" outlined>
          <div class="d-flex align-center">
          <div>
          <v-text-field
            class="my-n2"
            v-model="searchText"
            clearable
            prepend-icon="mdi-magnify"
            @click:clear="searchText = ''"
            >
          </v-text-field>
          </div>
          <v-btn
          icon
          @click="sortByLevel = ! sortByLevel"
          >
          <v-icon class="px-3" :color="sortByLevel ? 'primary' : '' "> mdi-priority-high </v-icon>
          </v-btn>
          <v-btn
          icon
          @click="sortByDate = ! sortByDate"
          >
            <v-icon :color="sortByDate ? 'primary' : '' "> mdi-sort-calendar-ascending
            </v-icon>
          </v-btn>
          </div>
        </v-card>
        </div>
        <div>

        <div v-for="(item,index) in articlesFiltered" :key="index">

        <v-card class="mx-auto my-7" >
          <v-list-item three-line class="text-start" >
              <v-list-item-content class="" >

                <v-list-item-title :class="item.expand ? 'mt-n6' : '' ">
                <div class="d-flex align-center">
                <div>
                  <v-icon :color="priorityColor(item.level)"> mdi-checkbox-blank-circle </v-icon>
                </div>
                <div class="px-2 text-truncate">
                {{item.title}}
                </div>
                <v-spacer/>
                <div class="d-flex align-center">
                      <div class="pr-2 grey--text"> {{showDate(item.published_date)}} </div>

                     <div>
                    <v-btn
                      icon
                      @click="item.expand = ! item.expand"
                      >
                      <v-icon>{{ item.expand ? 'mdi-chevron-up' : 'mdi-chevron-down' }}</v-icon>
                    </v-btn>
                      </div>
                </div>
                </div>

                </v-list-item-title>

                <v-list-item-subtitle v-show=" !item.expand " class="subtitle-2 grey--text">
                  <div>
                  {{item.body}}
                  </div>

                </v-list-item-subtitle>

                </v-list-item-content>

          </v-list-item>

          <v-expand-transition>
            <div v-show="item.expand" class="subtitle-2  grey--text text-start px-5 mt-n8 pb-2">
                <div>
                  {{item.body}}
                </div>
                <v-divider />
                    <v-list dense >
                <v-list-item v-for="(url, i) in item.url_action" :key="i">
                <v-list-item-content>
                    <v-list-item-title>
                      {{url.title}}
                    </v-list-item-title>
                    <v-list-item-subtitle>
                      {{url.url}}
                    </v-list-item-subtitle>
                </v-list-item-content>
                </v-list-item>
              </v-list>

              <v-divider />
              <v-list dense >
                <v-list-item v-for="(url, i) in item.url_explanation" :key="i">
                <v-list-item-content>
                    <v-list-item-title>
                      {{url.title}}
                    </v-list-item-title>
                    <v-list-item-subtitle>
                      {{url.url}}
                    </v-list-item-subtitle>
                </v-list-item-content>
                </v-list-item>
              </v-list>
            </div>

          </v-expand-transition>
      </v-card>
        </div>

        <div v-if="articlesFiltered.length == 0" class="mt-7 grey--text">
          Keine Suchergebnisse
        </div>

        <div>
        </div>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import InputData from '../../public/input.json'

export default {
  name: 'Content-view',

  data: () => ({
    inputData: [],
    searchText : '',
    sortByDate : false,
    sortByLevel : false
  }),

  computed : {

    articlesFiltered() {
    let items = this.inputData
    items = items.filter( el => el.title.toUpperCase().includes(this.searchText.toUpperCase())  ||  el.body.toUpperCase().includes(this.searchText.toUpperCase()))

    if ( this.sortByDate) {
    items = items.sort(function(a, b) {
        console.log(a.published_date)
        return a.published_date - b.published_date;
        });
      } else {
            items = items.sort(function(a, b) {
              return b.published_date - a.published_date;
              });
        }

    if ( this.sortByLevel) {
    items = items.sort(function(a, b) {
        return b.level - a.level;
        });
      }

      return items
      }

    },

  methods : {
    showDate(date){
      return new Date(date * 1000).toLocaleDateString()
      },

    priorityColor(level){
      let color=""
      if(level==1) color="green"
      if(level==2) color="yellow"
      if(level==3) color="red"
      return color
      },

//    fetchData(){
//     var headers = {}
//     fetch("https://www.sicher-im-netz.de/siba-app/siba/list/0", {
//         method : "GET",
//         mode: 'cors',
//         headers: headers
//     }).then(response => response.json())
//       .then(data => {
//         console.log(data)
//       });
//
//   },

    init(){
      console.log('init')
      InputData.forEach( el => {
        el.expand = false
        this.inputData.push(el)
        })
      },


    },

    mounted(){
      this.init()
//       this.fetchData()
      }

}
</script>
