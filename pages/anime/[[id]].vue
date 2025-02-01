<template>
    <div class="text-center">
        <Navigation />
        <div v-if="response.title">
            <div class=" flex justify-center">
                <Card class="w-full">
                    <template #content>
                        <div class=" flex justify-center">
                            <img :src="response.images.webp.image_url" :alt="response.title" />
                        </div>
                        <h2 class=" font-bold">{{ response.title }}</h2>
                        <span class=" mr-6">에피소드: {{  response.episodes }}</span>
                        <span>평점 {{ response.score }}</span>
                        <div>
                            장르: <span v-for="genre in response.genres">{{ genre.name }}</span>
                        </div>
                        <div>
                            <span v-for="studio in response.studios" class="mr-4">{{ studio.name }}</span>
                        <span> {{ response.airing ? 'Currently Airing' : "Finished" }}</span>
                         </div>
                    </template>
                </Card>
            </div>
            <div class=" w-full">
                <h3 class=" font-semibold">Synopsis</h3>
                <hr>
                <Card class=" min-w-full">
                    <template #content>
                        {{ response.synopsis }}
                    </template>
                </Card>
            </div>
            <div class=" h-48">
                <h3 class=" font-semibold">에피소드</h3>
                <hr>
                <button class=" px-5 py-3 mx-1 my-2 shadow-md rounded-md" v-for="ep in response.episodes"> {{ ep }}</button>
            </div>
            <div class=" h-108">
                <h3 class=" font-semibold">트레일러</h3>
                <hr>
                <div class="vid" v-if="trailerUrl">
                    <iframe 
                       v-if="trailerUrl"
                       width="1200"
                       height="600"
                       :src="trailerUrl"
                       frameborder="0"
                       allow="accelerometer; autoplay; encrypted-media; gyroscope; pictrue-in-picture"
                       allowfullscreen
                       class="vide"
                    ></iframe>
                </div>
                <div v-else>
                    <p>트레일러X</p>
                </div>
            </div>
        </div>
        <div v-else>
            <h1>로딩중...</h1>
        </div>
    </div>
</template>

<script setup>
   const router = useRoute();
   const response = ref([]);
   const trailerUrl = ref('');

   onBeforeMount(()=>{
       getItem()
   })
   onMounted(() => {
       getItem()
   })


    const getItem = async() => {
        try {
            const {data, error} = await useFetch(`https://api.jikan.moe/v4/anime/${router.params.id}/full`)
            
            if (!error.value) {
                response.value = data.value.data;
                // trailerUrl.value = response.value.trailer_url || `https://www.youtube.com/embed/${response.value.youtube_id}` || `https://api.jikan.moe/v4/anime/${episode.id}/videos` ;
                // trailerUrl.value = response.value.trailer_url ? `https://www.youtube.com/embed/${response.value.trailer_url.split('/')[4]}` : '';
                
               if(response.value.trailer && response.value.trailer.embed_url) {
                 trailerUrl.value = response.value.trailer.embed_url;
               } else {
                trailerUrl.value = '';
               }

                episodeArray.value = Array.form({ length: response.value.episodes }, (_,i) =>
                    i + 1);
            } else {
                trailer_url.value = '';
            }
        } catch(error) {
            console.log(error)
        }
    }
</script>

<style lang="scss" scoped>

 .vid {
    width:100%;
 
 }

 .vide {
    width:100%;
    max-width: 1300px;
    align-items: center;
    align-content: center;
    justify-content: center;
    align-self: center;
    margin:auto;
 }

 
</style>