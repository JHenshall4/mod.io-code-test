<script> 
import axios from "axios"

export default {
    name: "test",
    data(){
        return {
            games: [],
            show: false
        }
    },
    async created(){
        const convertDate = (date) =>{
            var newDate = new Date(date * 1000);
            return (newDate.toLocaleDateString("en-AU"));
        }

        const updateData = (arr) => {
            arr.forEach(game => {
                game["date_added"] = convertDate(game["date_added"]);
            })
        }

        try{
            var headers = {
                'Accept': 'application/json'
            }

            var params = {
                api_key: 'f0e1ce6078f8e8cbfc70821fcec04f56', // MUST OBFUSCATE THIS
            }

            const res = await axios.get('https://api.mod.io/v1/games', { headers: headers, params: params })
            //console.log(res.data);
            updateData(res.data.data);
            this.games = res.data.data;
        }
        catch (e){
            console.log(e)
        }
    }
}
</script>

<template>
    <div class="games_container" id="games_container">
        <div v-for="game in games" :key="game" class="game-box">
            <div class="game-wrapper">
                <img :src="game.logo.thumb_320x180" />
                <div class="title-date">
                    <div class="game-title">{{game.name}}</div>
                    <div class="game-date">{{game.date_added}}</div>
                </div>
                <div class="stats-box">
                    <div>S: {{game.stats.mods_subscribers_total}}</div>
                    <div>D: {{game.stats.mods_downloads_total}}</div>
                </div>
            </div>
        </div>
    </div>
</template>



<style scoped>
*{
    box-sizing:border-box;
}
a {
  color: #42b983;
}
/* todo: add the above styles globally */

.games_container{
    max-width:1000px;
    margin:0px auto;
    display:flex;
    flex-wrap:wrap;
    justify-content:space-between;
    padding:100px 0;
}

.game-box{
    flex-basis:32%;
    margin-bottom:25px;
    text-align:left;
}

.game-wrapper{
    display:flex;
    flex-direction:column;
    background-color:#e0e0e0;
    justify-content:space-between;
}

.game-wrapper img{
    width:100%;
    height:200px;
}

.title-date{
    display:flex;
    justify-content:space-between;
    padding:10px 5px;
    min-height:100px;
}

.game-title{
    display:block;
    font-size:18px;
    font-weight:600;
}

.stats-box{
    display:flex;
    justify-content:space-between;
    background-color:#d4d4d4;
}

.game-date{
    font-size:12px;
}

@media screen and (max-width:1000px){
    .game-box{
        flex-basis:48%;
    }
}

@media screen and (max-width:767px){
    .game-box{
        flex-basis:100%;
    }
}

</style>
