<script setup>
import { computed, ref, watch } from 'vue'
import { posterUrl } from '../api.js'

const props = defineProps({ movie: { type: Object, required: true } })
const imageFailed = ref(false)
const imageUrl = computed(() => posterUrl(props.movie.poster_path))
const rating = computed(() => props.movie.vote_count > 0
  ? Number(props.movie.vote_average).toFixed(1) : 'NR')
const year = computed(() => props.movie.release_date?.slice(0, 4) || '')
watch(imageUrl, () => { imageFailed.value = false })
</script>

<template>
  <article class="movie-card">
    <div class="poster-wrap">
      <img v-if="imageUrl && !imageFailed" class="movie-poster"
        :src="imageUrl" :alt="`${movie.title} poster`" loading="lazy"
        width="500" height="750" @error="imageFailed = true">
      <div v-else class="poster-fallback">
        <span>No poster available</span>
        <strong>{{ movie.title }}</strong>
      </div>

      <!-- Header-ийн nav шиг булан таслагдсан шилэн таб -->
      <span class="rating" :aria-label="rating === 'NR' ? 'Not rated' : `Rating ${rating} out of 10`">
        {{ rating }}<small v-if="rating !== 'NR'">/10</small>
      </span>
    </div>
    <div class="movie-info">
      <p class="genre">TMDB MOVIE<template v-if="year"> / {{ year }}</template></p>
      <h3>
        <a :href="`https://www.themoviedb.org/movie/${movie.id}`" target="_blank" rel="noopener">{{ movie.title }}</a>
      </h3>
      <time v-if="movie.release_date" :datetime="movie.release_date">{{ movie.release_date }}</time>
      <span v-else class="unknown-date">Release date unknown</span>
      <p class="overview">{{ movie.overview || 'No overview available.' }}</p>
    </div>
  </article>
</template>

<style scoped>
.movie-card{
  --cut:26px;
  position:relative;
  display:flex;flex-direction:column;
  filter:drop-shadow(0 14px 18px rgba(0,0,0,.35));
  transition:transform .25s ease,filter .25s ease;
}
.movie-card:hover,
.movie-card:focus-within{transform:translateY(-4px);filter:drop-shadow(0 18px 24px rgba(0,0,0,.5))}
.poster-wrap{
  position:relative;overflow:hidden;
  clip-path:polygon(var(--cut) 0,100% 0,100% 100%,0 100%,0 var(--cut));
}
.movie-poster,
.poster-fallback{border-radius:0;transition:transform .35s ease}
.poster-fallback{background:linear-gradient(155deg,#2c2f3a,#0d0f16);color:var(--muted)}
.movie-card:hover .movie-poster,
.movie-card:focus-within .movie-poster{transform:scale(1.04)}
.rating{
  position:absolute;top:0;right:0;z-index:2;
  width:auto;height:auto;border:0;border-radius:0;
  display:flex;align-items:baseline;gap:2px;
  padding:11px 14px 15px 26px;
  background:var(--glass-strong);backdrop-filter:blur(6px);
  color:#fff;font-size:15px;font-weight:800;letter-spacing:.3px;
  clip-path:polygon(0 0,100% 0,100% 100%,16px 100%,0 55%);
}
.rating small{font-size:9px;margin:0;color:var(--accent);font-weight:700}
.movie-info{
  position:relative;z-index:1;
  margin:-40px 12px 0;
  padding:20px 22px 30px 20px;
  background:linear-gradient(160deg,rgba(80,80,84,.78),rgba(18,20,28,.92));
  backdrop-filter:blur(8px);
  clip-path:polygon(0 0,100% 0,100% calc(100% - var(--cut)),calc(100% - var(--cut)) 100%,0 100%);
  transition:background .25s ease;
}
.movie-card:hover .movie-info,
.movie-card:focus-within .movie-info{
  background:linear-gradient(160deg,rgba(90,96,120,.85),rgba(18,20,28,.94));
}

.genre{margin:0 0 12px;font-size:9px;font-weight:700;letter-spacing:1.6px;color:var(--muted)}

.movie-info h3{margin:0 0 10px;font-size:17px;line-height:1.75;letter-spacing:-.2px}
.movie-info h3 a{
  background:rgba(255,255,255,.2);
  padding:.12em .45em;
  -webkit-box-decoration-break:clone;box-decoration-break:clone;
  text-decoration:none;
  transition:background .2s ease;
}
.movie-info h3 a:hover{text-decoration:none;background:color-mix(in srgb,var(--accent) 40%,transparent)}

.movie-info time,.unknown-date{display:block;font-size:11px;color:var(--muted);letter-spacing:.4px}
.overview{
  margin:14px 0 0;padding-left:12px;
  border-left:2px solid var(--accent);
  font-size:12.5px;line-height:1.6;color:#d3d6db;
}

@media(max-width:700px){
  .movie-card{--cut:18px}
  .movie-info{margin:-30px 6px 0;padding:16px 16px 24px 14px}
  .movie-info h3{font-size:15px}
  .rating{padding:9px 11px 13px 20px;font-size:13px}
  .overview{font-size:12px}
}
@media(prefers-reduced-motion:reduce){
  .movie-card,.movie-poster,.poster-fallback,.movie-info,.movie-info h3 a{transition:none}
  .movie-card:hover,.movie-card:focus-within{transform:none}
  .movie-card:hover .movie-poster,.movie-card:focus-within .movie-poster{transform:none}
}
</style>
