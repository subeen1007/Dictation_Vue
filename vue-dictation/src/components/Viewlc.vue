<template>
  <v-card class="mx-auto">
    <v-toolbar
      color="purple"
      dark
      flat
    >
    

      <template v-slot:extension>
        <v-tabs
          v-model="tabs"
          centered
        >
          <v-tab
            v-for="courseTab in courseTabs"
            :key="courseTab"
          >
            {{courseTab}}
          </v-tab>
        </v-tabs>
      </template>
    </v-toolbar>
 
          
       
    <v-tabs-items v-model="tabs">
      
      <v-tab-item>
        <v-card>
          <v-card-title>
             <v-select
              class="float-left mt-6"
              v-model="searchs"
              :items="searchs"
              label="검색조건"
            ></v-select>
            <v-spacer></v-spacer>
            <v-text-field
              v-model="search"
              label="Search"
              single-line
              hide-details
            ></v-text-field>
            <v-btn icon color="indigo" class="pt-4">
              <v-icon>mdi-magnify</v-icon>
            </v-btn>
          </v-card-title>
          <!-- 강좌 리스트-->
          <v-data-table 
            :headers="headers"
            :items="lectures"
            :search="search"
            
          >
        <template v-slot:item="row">
          <tr>
            <td>{{row.item.lecture_no}}</td>
            <td>{{row.item.lecture_nm}}</td>
            <td>{{row.item.grade}}</td>
            <td>{{row.item.teacher_nm}}</td>
            <td>{{row.item.enroll_ed_dt}}</td>
            <td>
              <v-btn v-if="row.item.approval_cd === '미승인'" class="mr-5" small color="error" @click="cancel(row.item)">신청취소</v-btn>
              <v-btn v-else-if="row.item.approval_cd === '승인'" class="mr-6" small color="success">승인완료</v-btn>
              <v-btn v-else small color="primary" @click="request(row.item)">신청하기</v-btn>
            </td>
        </tr>
        </template>
        </v-data-table>
        </v-card>
      </v-tab-item>
     <v-tab-item>
          <v-card class="mx-auto">
    <v-card-title>
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="검색어를 입력하세요"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <!-- 개설 강좌-->
    <v-data-table
      :headers="headers2"
      :items="mylectures"
      :search="search2"
    >
    <template v-slot:item="row"><!--이렇게 해야td안에 들어감-->
        <tr>
          <td>{{row.item.lecture_no}}</td>
          <td>{{row.item.lecture_nm}}</td>
          <td>{{row.item.grade}}</td>
          <td>{{row.item.teacher_nm}}</td>
          <td>{{row.item.enroll_ed_dt}}</td>
          <td>
            <v-btn small color="primary" dark class="ml-5" @click="gotmain(row.item)">강좌로 들어가기</v-btn>
          </td>
        </tr>
    </template>
    </v-data-table>
    </v-card>
    </v-tab-item>
    </v-tabs-items>
  </v-card>
</template>

<script>
import router from '../router'
  export default {
    
    data () {
      return {
        tabs: null,
        searchs:["제목","선생님","학년"],
        searchs2:["","선생님","학년"],
        search: '',
        courseTabs: ["강좌리스트", "개설강좌"],
        headers: [
          { text: '강좌코드', value: 'lecture_no' },
          { text: '강좌명', value: 'lecture_nm' },
          { text: '학년', value: 'grade' },
          { text: '선생님', value: 'teacher_nm' },
          { text: '신청기간', value: 'enroll_ed_dt' },
          { text: '신청여부', value: 'actions', sortable: false },
        ],
        headers2: [
          { text: '강좌코드', value: 'lecture_no' },
          { text: '강좌명', value: 'lecture_nm' },
          { text: '학년', value: 'grade' },
          { text: '선생님', value: 'teacher_nm' },
          { text: '신청기간', value: 'enroll_ed_dt' },
          { text: '강좌선택', value: 'actions', sortable: false },
        ],
        lectures:[

        ],
        mylectures:[

        ],

      }
    },
    created(){
    this.$http.post('/api/student/lecture/student_lec_list').then(res =>{
          console.log('status code: ${res.ban}');
          this.lectures=res.data;
          //console.log(res);
          //alert(JSON.stringify(this.lectures));
        
    })
    this.$http.post('/api/student/lecture/student_mylec').then(res =>{
          //console.log('status code: ${res.ban}');
          this.mylectures=res.data;
          //console.log(res);
          //alert(JSON.stringify(this.lectures));
    })
  },
    methods: {
      gotmain(item){
        this.$http.get(`/api/common/lecture/lecture_no/${item.lecture_no}`).then(res =>{
          console.log(res);
        })
        //console.log(item.lecture_no);
        router.push({name: 'studentlc'});
      },
      // 신청취소 버튼
      cancel(item){
        this.$http.get(`/api/student/enroll/delete/${item.lecture_no}`).then(res =>{
          console.log(res);
        })
      },
      //신청완료 버튼
      request(item){
        this.$http.get(`/api/student/enroll/insert/${item.lecture_no}`).then(res =>{
          console.log(res);
        })
      }
      
     
    }
  }
</script>