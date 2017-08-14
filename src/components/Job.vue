<template>
    <div>
      <h2>Find your job</h2>
      <ul id="showData">
        <li v-for="item in items" class="jobCard">
          <div class="jobTitle">{{ item.jobTitle }}</div>
          <div class="companyName">{{ item.companyName }}</div>
          <div class="jobDescription" v-html="item.jobDescription"></div>
        </li>
      </ul>
    </div>
</template>


<script>
import axios from 'axios'
const url = 'https://www.zippia.com/api/jobs/?title=Business%20Analyst&state=&city=&major=&useFallback=false'
let page = 1
let perPage = 40
let newArryC = []                                    // Store new array that companyInitial === 'C'
let totalPage
export default {
  name: 'Job',
  created () {
    this.getDataC()
  },
  methods: {
    getDataC: function () {
      if (newArryC.length >= 10) {
        this.$data.items = newArryC                    // add data to state machine
        console.log(newArryC)
        return 0
      }
      axios.get(url, {params: {page_no: page, items_per_page: perPage}})
      .then(res => {
        // console.log(res)
        if (res.status === 200) {
          page++
          // console.log(res.data)
          if (!totalPage) {
            totalPage = parseInt(res.data.totalJobs / perPage)         // calculate the totalPage
            if (res.data.totalJobs % perPage !== 0) {
              totalPage++
            }
          }
          if (page >= totalPage) {
            this.$data.items = newArryC                    // add data to state machine
            return 0
          }
          res.data.jobs.map((item) => {
            // console.log(item, totalPage)
            if (item.companyInitial === 'C' && newArryC.length < 10) {                 // Cycle to find companyInitial === 'C'
              newArryC.push(item)
            }
          })
        }
        this.getDataC()
      })
      .catch(error => {
        console.log(error)
        this.getDataC()
      })
    }
  },
  data () {
    return {
      items: ''
    }
  }
}
</script>

<style scope>
    ul{padding: 0;} 
    li{
      list-style:none;
    }
    .jobCard{
      transition-duration: 0.5s;
      background: #eaeaea;
      width: 80%;
      margin: 0 auto;
      padding: 10px;
      margin-bottom: 19px;
    }
    .jobCard:hover{
      box-shadow: 10px 10px 5px #888888;
    }
    .jobTitle{
      font-size: 20px;
      font-weight: bold;
    }
    .companyName{
      font-size: 15px;
    }
    .jobDescription{
      text-align: left;
      font-size: 18px;
    }
    .jobDescription p{
      text-align: left !important;
    }
</style>