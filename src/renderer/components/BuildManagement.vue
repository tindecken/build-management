<template>
  <div>
    <div class="row">
      <div class='col'>
        <h4>Build Management</h4>
      </div>
    </div>
    <div class='row'>
        <table class="bordered highlight">
          <thead>
            <tr>
              <th>No.</th>
              <th>ID</th>
              <th>Build Name</th>
              <th>Last Build</th>
              <th>Action</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="(n, index) in value" :key="n.id">
              <td>{{index + 1}}</td>
              <!-- <td><a v-on:click="openLink(value[n-1]._links.web.href)">{{value[n-1].id}}</a></td>  -->
              <td><a v-on:click="openLink(n._links.web.href)">{{n.id}}</a></td> 
              <td>{{n.name}}</td>
              <td>Eclair</td>
              <td>$0.87</td>
            </tr>
          </tbody>
        </table>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  const {shell} = require('electron')
  export default {
    data: () => ({
      definitionsID: [],
      value: [],
      count: 1,
      lastResult: [],
      authen:{
        username: 'thaihoang.nguyen@ezitsol.com',
        password: '1Rivaldo@'
      }
    }),
    methods: {
      openLink: (url) => {
        console.log('URL: ' + url)
        shell.openExternal(url)
      },
      getLasBuildStatus: (buildDefinitionId) => {
        results: []
        axios.get(`https://acomsolutions.visualstudio.com/DefaultCollection/AutoTestManagement_Tool/_apis/build/builds?api-version=2.0&definitions=${buildDefinitionId}&$top=1`, {
          auth: {
            username: 'thaihoang.nguyen@ezitsol.com',
            password: '1Rivaldo@'
          }
          })
          .then((response) => {
            // this.results.push("AAAAAAAAAAAa")
            console.log("status: " + response.data.value[0] && response.data.value[0].result ? response.data.value[0].result : "failed")
          })
          .catch((error) => {
            console.log(error)
          })
        }
      },
    computed: {
    },
    created() {
      const vm = this
      axios.get('https://acomsolutions.visualstudio.com/DefaultCollection/AutoTestManagement_Tool/_apis/build/definitions?api-version=2.0', {
        auth: {
          username: 'thaihoang.nguyen@ezitsol.com',
          password: '1Rivaldo@'
        }
      })
      .then((response) => {
        console.log(response.data.count)
        vm.count = response.data.count
        vm.value = response.data.value
        console.log("AA" + vm.value.length);
        vm.value.forEach((currentValue, index, arr)=> {
          setTimeout(()=>{
            console.log("AAAAAAAAAAAAAA:" + currentValue.id);
            this.getLasBuildStatus(currentValue.id)
          }, 2000)
          
        })
        // for(var i=0; i <= vm.value.length; i++){
        //   console.log("Value child:" + vm.value[i].id);
        //   this.getLasBuildStatus(vm.value[i].id)
        //   // console.log(`Last result of definitions ${value.id} is ${value.result}`)
        // }
        // vm.definitionsID = response.data.value
      })
      .catch((error) => {
        console.log(error)
      });
    } 
  }
</script>
<style>
  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  body { font-family: 'Source Sans Pro', sans-serif; }
</style>
