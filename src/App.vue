<template>
    <router-view></router-view>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core'
import { faArrowRight, faArrowLeft } from '@fortawesome/free-solid-svg-icons'
library.add(faArrowRight, faArrowLeft)

export default {
    name: "App"  
}

</script>