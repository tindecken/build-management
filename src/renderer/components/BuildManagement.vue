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
      lastStatus:[],
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
      }
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
        console.log("Number of value object: " + vm.value.length);
        var index = 0;
        loop();
        function loop() {
          if (index == vm.value.length) return;
          var id_temp = vm.value[index].id
          this.lastStatus.push('sdfsdf');
          axios.get(`https://acomsolutions.visualstudio.com/DefaultCollection/AutoTestManagement_Tool/_apis/build/builds?api-version=2.0&definitions=${id_temp}&$top=1`, {
            auth: {
              username: 'thaihoang.nguyen@ezitsol.com',
              password: '1Rivaldo@'
            }
            })
            .then((response) => {
                if (response.data.value && response.data.value[0]) {
                    lastStatus.push(response.data.value[0].status);
                    if(response.data.value[0].result){
                        console.log(id_temp + '-' + response.data.value[0].result);
                        this.lastResult.push(response.data.value[0].result);
                    }else{
                        console.log(id_temp + '-' + response.data.value[0].status)
                        this.lastResult.push(response.data.value[0].status);
                    }
                }else{
                    console.log(id_temp + '-' + 'not found any build')
                    this.lastStatus.push('')
                    this.lastResult.push('');

                }
            //   console.log("status: " + response.value[0] && response.value[0].result ? response.value[0].result : "notfound")
            //   responses.push(response.result)
                index++;
                loop();
            })
            .catch((error) => {
                console.log('Error' + error)
            })
        }
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
