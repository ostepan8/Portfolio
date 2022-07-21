<template>
    <div class="bg-black h-screen px-6 py-12">
        <h2 class="text-white text-5xl opacity-0" ref="title">My Work</h2>
        <div class="flex space-x-8">    
            <div style ="transform: translateY(100%);"ref="projects" v-for ="project in projects" class="opacity-0">
                <nuxt-link to ="/">
                    <img 
                        :src ="project.image.url"
                        alt="iPhone"
                        style="height:70vh" 
                        class="object-cover"
                    >
                    <p class="text-white text-sm">{{project.title}}</p>
                </nuxt-link>
            </div>
        </div>
    </div>
</template>

<script>
import gsap from 'gsap'
    import threeImg from '~/assets/three-js-img.jpg'
    import webImg from '~/assets/web-dev-img.jpg'
    import gameDev from '~/assets/game-dev-img.jpg'
export default{
    data(){
        return{
            projects:[
                {
                    image: {
                        url:webImg
                    },
                    title: "Three JS"
                },
                {
                    image: {
                        url:threeImg
                    },
                    title: "Web Dev"
                },
                {
                    image: {
                        url:gameDev
                    },
                    title: "Game Dev"
                },
            ]
        }
    },
    mounted(){
        gsap.to(this.$refs.title,{
            opacity:1,
            duration: 2
        })
        gsap.to(this.$refs.projects,{
            opacity:1,
            duration:2,
            stagger: 0.1,
            y:0,
            ease:'expo' 
        })
    }
}
</script>
