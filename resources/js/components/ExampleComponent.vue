<template>
    <div class="container">
        <div class="row ">
            <div class="col-sm-12">
                <div class="card-header text-center">APOLLO LARAVEL PROJECT</div>

                <div class="float-left col-sm-6 border">
                    <div class="card-header margin-bot-30">TRY ME ?</div>
                    <div class='col-sm-12 text-center margin-top-20'><span>*Please input 10 random names</span></div>
                    <div class="input-group mb-3 margin-top-20">
                      <input type="text" class="form-control "v-on:keyup.enter="addRandoms" v-model="randoms" placeholder="Please Input Random Names" aria-label="Recipient's username" aria-describedby="button-addon2">
                      <button class="btn btn-outline-secondary" v-on:click="addRandoms" type="button" id="button-addon2">Add</button>
                    </div>
                    <div class="col-sm-12 text-center">{{ randomArr }}</div>
                    <div class="col-sm-12 text-center">
                      
                      <button class="btn btn-primary margin-20" type="button" id="button-addon2" v-on:click="generateRandoms">GENERATE RANDOM NAMES AND BREAKDOWN</button>
                    </div>
                    

                </div>
                <div class=" float-right col-sm-6 border">
                  <div class="card-header">DISPLAY BREAKDOWNS</div>
                    <div id="container">  
                      <svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" preserveAspectRatio="xMidYMin slice" viewBox="0 0 728 400">
                        <!--Background-->  
                        <path d="M0 0h728v400H0z" fill="#fff"/> 
                        <!--Spiral path-->  
                        <defs>
                          <path id="s" d="M363.32 203.973c3.65 3.65-3.119 6.72-6.066 6.066-7.986-1.773-9.27-12.002-6.066-18.198 5.731-11.082 20.612-12.38 30.33-6.065 14.26 9.267 15.584 29.339 6.065 42.46-12.686 17.49-38.107 18.828-54.592 6.067-20.745-16.06-22.09-46.897-6.066-66.725 19.408-24.015 55.695-25.365 78.856-6.066 27.294 22.744 28.648 64.502 6.066 90.988-26.071 30.58-73.313 31.935-103.12 6.066-33.869-29.394-35.225-82.127-6.066-115.252 32.713-37.16 90.944-38.518 127.384-6.065 40.455 36.028 41.813 99.762 6.065 139.515-39.342 43.75-108.581 45.11-151.646 6.065-47.048-42.655-48.408-117.402-6.066-163.778 45.966-50.346 126.224-51.706 175.91-6.066 53.645 49.277 55.005 135.047 6.066 188.042-52.587 56.945-143.87 58.305-200.174 6.066-60.244-55.895-61.605-152.693-6.066-212.306 59.204-63.545 161.518-64.906 224.438-6.065 53.59 50.116 66.879 131.92 33.787 197.072" />
                        </defs>  
                        <!--Text & link to path-->  
                        <text maxlength="4000" font-family="monospace" font-size="20" fill="#1d1f20" >
                        <textPath  id="text" xlink:href="#s">{{spiralText}}</textPath>
                        </text>  
                      </svg> 
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        mounted() {

            this.getBreakdowns()
        },
        data(){
          return{
            randomNumber:this.getRandomInt(5,10),
            spiralText:'',
            randoms:'',
            randomArr:[],
            
          }
          
        },
        methods:{
            getRandomInt(min, max) {
              return Math.floor(Math.random() * (max - min + 1)) + min;
            },
            addRandoms:function(e){
              // console.log(this.randoms);
              this.randomArr.push(this.randoms);
              this.randoms = null;
            },
            getBreakdowns:function(){
               axios.get("/api/getBreakdown" ).then((res) => {
                 var text = ''
                 for (let i = 0; i < res.data.length; i++) {
                   text += ' '+res.data[i].values
                   
                 }
                 console.log(text);
                  return this.spiralText = text;
                
               });
            },
            genBreakChars:function(length){
              var result           = '';
              var char       = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
              var charLength = char.length;
              for ( var i = 0; i < length; i++ ) {
                  result += char.charAt(Math.floor(Math.random() * charLength));
              }
              return result;
            },
            genRandomNames:function(arr){
               return arr[this.getRandomInt(0,arr.length)];
            },
            generateRandoms(){
              
              for(var i =0 ; i < this.randomNumber;i++){
                var name = this.genRandomNames(this.randomArr);
                axios.post("/api/pushRandom",{'values':name}).then((res) => {
                  if(res){
                    this.spiralText = '';
                    for(var j =0 ; j < this.randomNumber;j++){
                        console.log(name,this.genBreakChars());
                        axios.post("/api/pushBreak",{'values':this.genBreakChars(5),'random_id':res.data.id})
                        .then((res2) => {
                          
                          this.spiralText += ' '+res2.data.values;
                          // console.log(res2);
                        })
                        
                    }
                  }
                   

                })
                .catch((err)=> {
                    console.log(err);
                });
                
              }
              
            },
            
        }
    }
</script>

<style scoped lang="css">
  .margin-20{
    margin:20px;
  }
  .margin-top-20 {
    margin-top: 20px !important;
}
.margin-bot-20 {
    margin-bottom: 20px !important;
}

</style>
