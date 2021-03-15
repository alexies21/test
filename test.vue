<template>
    <div class="container-fluid">
        <div class="row justify-content-center">
          <div id="overlay" v-if="loading">
            <tile style="color:white"></tile>
            <center ><p class="text-light h6 centerthis">LOADING PLEASE WAIT..</p></center>
          </div>
            <div class="col-md-12">

                <div class="card">
                  <div class="card-header bg-dark text-white float-left font-weight-bold">
                    List of Cities on the United States
                    <a class="btn btn-sm btn-success float-right" @click.prevent='showaddmodal'>Add City</a>
                  </div>
                    <div class="card-body" style="padding:0">
                        <table class="table table-hover table-striped">
                          <thead>
                            <tr>
                              <th>State Name</th>
                              <th>Action</th>
                            </tr>
                          </thead>
                          <tbody>
                            <tr v-for="(t, index) in states" :index='index'>
                              <td>{{t.state_name}}</td>
                              <td><a class="btn btn-info btn-sm text-white" @click.prevent='showupdatemodal(index, t)'>Update</a> <a class="btn btn-danger btn-sm" @click.prevent='deletecity(index,t)'>Delete</a></td>
                            </tr>
                          </tbody>
                        </table>
                    </div>
                </div>

                <!-- modal for update -->
                <div class="modal fade" id="modalcity" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
                  <div class="modal-dialog modal-dialog-centered" role="document">
                    <div class="modal-content">
                      <div class="modal-header bg-dark">
                        <h5 class="modal-title text-white" id="exampleModalLongTitle">Update City</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                          <span aria-hidden="true">&times;</span>
                        </button>
                      </div>
                      <div class="modal-body">
                        <input type="text" class="form-control" v-model="state_name">
                      </div>
                      <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                        <button type="button" class="btn btn-primary" @click.prevent='updatecity' v-if="mode==='update'">Update Changes</button>
                        <button type="button" class="btn btn-primary" @click.prevent='addcity' v-if="mode==='add'">Add City</button>
                      </div>
                    </div>
                  </div>
                </div>
                <!--end of modal for update -->
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data(){
          return{
            loading:false,
            states:[],
            token:'',
            state_name:'',
            index:'',
            mode:''
          }
        },
        methods:{
          addcity(){
            $('#modalcity').modal('hide');
            Swal.fire({
              title: 'Please Confirm',
              text: "That you want to add "+this.state_name,
              icon: 'warning',
              showCancelButton: true,
              confirmButtonColor: '#3085d6',
              cancelButtonColor: '#d33',
              confirmButtonText: 'Confirm'
            }).then((result) => {
              if (result.isConfirmed) {
                if (this.state_name) {
                  this.states.push({index:this.states.length+1, state_name: this.state_name });
                  Swal.fire(
                    'Success',
                    'City has been posted.',
                    'success'
                  );
                }else {
                  Swal.fire(
                    'Ooppss',
                    'Please fill up the form.',
                    'warning'
                  );
                }
              }
            })
          },
          showaddmodal(){
            this.mode = 'add';
            this.state_name = '';
            this.index = '';
            $('#modalcity').modal('show');
          },
          deletecity(index,t){
            Swal.fire({
              title: 'Please Confirm',
              text: "That you want to delete "+t.state_name,
              icon: 'warning',
              showCancelButton: true,
              confirmButtonColor: '#3085d6',
              cancelButtonColor: '#d33',
              confirmButtonText: 'Confirm'
            }).then((result) => {
              if (result.isConfirmed) {
                this.states.splice(index, 1);
                Swal.fire(
                  'Deleted!',
                  'Your file has been deleted.',
                  'success'
                )
              }
            })
          },
          updatecity(){
            $('#modalcity').modal('hide');
            Swal.fire({
              title: 'Please Confirm',
              text: "That you want to update data?",
              icon: 'warning',
              showCancelButton: true,
              confirmButtonColor: '#3085d6',
              cancelButtonColor: '#d33',
              confirmButtonText: 'Confirm'
            }).then((result) => {
              if (result.isConfirmed) {
                this.states[this.index].state_name = this.state_name;
                Swal.fire(
                  'Confirmed',
                  'City has been updated.',
                  'success'
                )
              }else {
                $('#modalcity').modal('show');
              }
            })
          },
          showupdatemodal(index, t){
            this.mode = 'update';
            this.index = index;
            this.state_name = t.state_name;
            $('#modalcity').modal('show');
          },
          gettokens(){
            this.loading = true;
            axios.get('https://www.universal-tutorial.com/api/getaccesstoken', {
              headers: {
                "Accept": "application/json",
                'api-token': `49IfTAhuzSEURu8G1RztHQWnCCsw0-RNK5Sytxz4yJ-snVioQU69UeB0j7jL-tyLWW0`,
                "user-email": "nicole.lorezca@gmail.com",
              }
            })
            .then(response => {
              this.token = response.data.auth_token;
              this.getdata();
            })
          //
        },
        getdata(){

          axios.get('https://www.universal-tutorial.com/api/states/United States',{
            headers:{
              'Authorization' : 'Bearer '+this.token,
              "Accept": "application/json",
            }
          }).then(response=>{
            this.loading = false;
            this.states = response.data
          })
        }
        },
        created() {
          this.gettokens();
        }
    }
</script>
