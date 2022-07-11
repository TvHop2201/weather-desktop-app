<template>
<div>
    <section class="vh-100 div-goc">
    <div class="container py-5 h-100  ">
        <div class="row d-flex justify-content-center align-items-center h-100">
        <div class="col-md-10 col-lg-8 col-xl-6">

            <div class="card shadow" style="border-radius: 35px;" >
            <div class="card-body p-4 text-center">
                <i class="fa fa-search" aria-hidden="true" @click="getWeather()"></i>
                <input type="text" class="input-seach mx-auto" placeholder="Tìm Kiếm" v-model="location" @keyup.enter="getWeather()">
                <i class="fa fa-location-arrow" aria-hidden="true"></i>
            </div> 
            <div class="card-body p-4" v-if="!loading">
                <div class="d-flex m-5">
                <h3 class="flex-grow-1">{{city}}</h3>
                <h4>{{ new Date().getHours() +' : '+ new Date().getMinutes()}}</h4>
                </div>

                <div class="d-flex flex-column text-center mt-5 mb-4">
                <h6 class="display-4 mb-0 font-weight-bold" style="color: #1C2331;"> {{temp}}°C </h6>
                <div class="mt-5">
                    <img :src="`http://openweathermap.org/img/wn/${cloud.icon || '10d'}@4x.png`" width="100px">
                </div>
                <span class="small" style="color: #868B94">{{cloud.description || 'Nhiều Mây'}}</span>
                </div>

                <hr className='m-5' />


                <div class="d-flex align-items-center">
                    <div className='row m-5'>
                        <div className='col-6 flex-grow-1 item1'>
                            <div>
                                <p className='text-center'><i class="fas fa-sun fa-fw" style="color: #868B94;"></i> Mặt Trời Mọc : {{sunrise}}</p>
                            </div>
                            <div>
                                <p className='text-center'><i class="fas fa-sun fa-fw" style="color: #868B94;"></i> Mặt Trời Lặn : {{sunset}}</p>
                            </div>
                        </div>
                        <div className='col-4'>
                            <div>
                                <p><i class="fas fa-tint fa-fw" style="color: #868B94;"></i> Độ ẩm : {{hum}}</p>
                            </div>
                            <div>
                                <p><i class="fas fa-wind fa-fw" style="color: #868B94;"></i>Tốc độ gió : {{wind}} km/h</p>
                            </div>
                        </div>
                    </div>
                
                </div>

            </div>
            <div v-if="loading" class="text-center">
                <img src="../assets/loading.gif" class="mx-5" alt="">
            </div>
            </div>

        </div>
        </div>

    </div>
    </section>
</div>

</template>

<script>
import axios from 'axios'
import moment from 'moment'
export default {
    data(){
        return ({
            Weather_key:'b1ac492954c5e04bdb2ff86ca85b8de7',
            opencage_key:'99bd905cc7d0403380d7a0275ce7d2c5',
            kinhdo: 0,
            vido:0,
            location : '',
            city:'Vinh',
            temp :22,
            cloud:{},
            sunrise:'',
            sunset :'',
            hum: '',
            wind :'',
            loading: false
        })
    },
    async created(){
        this.loading = true
        let weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=vinh&appid=${this.Weather_key}&lang=vi&units=metric`)
        this.city = weatherData.data.name.toUpperCase()
        this.temp =weatherData.data.main.temp.toFixed()
        this.cloud = weatherData.data.weather[0]
        this.sunrise = moment.unix(weatherData.data.sys.sunrise).format('H:mm')
        this.sunset = moment.unix(weatherData.data.sys.sunset).format('H:mm')
        this.hum = weatherData.data.main.humidity +'%'
        this.wind = (weatherData.data.wind.speed * 3.6).toFixed(2)
        this.loading = false
    },
    methods:{
        async locationWeather(){
            const getLocation =(position)=>{
                this.vido = position.coords.latitude
                this.kinhdo = position.coords.longitude
            }
            navigator.geolocation.getCurrentPosition(getLocation,(err)=>alert(err.message))
            let data =await axios.get(`https://api.opencagedata.com/geocode/v1/json?q=${this.vido}+${this.kinhdo}&key=${this.opencage_key}`)
            console.log(data.data)
        },
        async getWeather(){
            if(this.location === ''){
                alert('Mời Nhập Địa Chỉ !!!')
                return
            }
            this.loading = true
            let location = encodeURI(this.location)
            let weatherData = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${location}&appid=${this.Weather_key}&lang=vi&units=metric`)
            if(weatherData.status=== 200){
                this.city = weatherData.data.name.toUpperCase()
                this.temp =weatherData.data.main.temp.toFixed()
                this.cloud = weatherData.data.weather[0]
                this.sunrise = moment.unix(weatherData.data.sys.sunrise).format('H:mm')
                this.sunset = moment.unix(weatherData.data.sys.sunset).format('H:mm')
                this.hum = weatherData.data.main.humidity +'%'
                this.wind = (weatherData.data.wind.speed * 3.6).toFixed(2)
            }else{
                alert('Không Tìm Thấy Địa Chỉ Này !!!!')
            }
            this.loading = false
        }
    }

}
</script>

<style>
.div-goc{
     background-image: url('https://img.freepik.com/free-photo/beautiful-aerial-shot-forest-enveloped-creepy-mist-fog_181624-2659.jpg?t=st=1649082374~exp=1649082974~hmac=344337f2e394b6edc84c10b7045f19c8bd249bd73605ef12860a76c9066ba9e9&w=1380');
}
.card{
    background-color: rgba(255, 255, 255, 0.514);
    
}
.input-seach {
    border: 0;
    width: auto;
    padding-left: 2em;
    padding-right: 50%;
    justify-content: center;
    align-items: center;
    background: transparent;
}

.input-seach:focus {
    outline: none;
}

.input-seach::placeholder {
    color: rgba(26, 23, 23, 0.308);
}

.input-seach:hover {
    border: 0;
}


</style>