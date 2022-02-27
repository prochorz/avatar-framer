<template>
    <div class="main__step main__step--4">
        <div class="main__step___message">
          <span class="main__step__count">4</span>
          <div class="main__step__text">Поделитесь в соц сетях и создайте мир над головами</div>
        </div>
        <div class="main__step__share">
          <div class="main__step__share--item">
             <ShareNetwork
                network="vk"
                :url="LINK"
                :title="SOCIAL_TITLE"
                :description="SOCIAL_DESCRIPTION"
            >
                <img :src="vk_share" alt="">
            </ShareNetwork>
          </div>
          <div class="main__step__share--item">
            <ShareNetwork
                network="facebook"
                :url="LINK"
                :title="SOCIAL_TITLE"
                :description="SOCIAL_DESCRIPTION"
            >
                <!-- <img :src="getImageUrl('share/fb_share','svg')" alt=""> -->
            </ShareNetwork>
          </div>
          <div class="main__step__share--item">
             <ShareNetwork
                network="odnoklassniki"
                :url="LINK"
                :title="SOCIAL_TITLE"
                :description="SOCIAL_DESCRIPTION"
            >
                <!-- <img :src="getImageUrl('share/od_share','svg')"  alt=""> -->
            </ShareNetwork>
          </div>
        </div>
      </div>
</template>

<script>
import { defineComponent } from 'vue';

import vk_share from '../../assets/img/share/vk_share.svg';
console.log(vk_share)
// import { defineComponent } from 'vue';
// import { defineComponent } from 'vue';

export default defineComponent({
    name: 'Social',
    setup() {
        const LINK = window.location.href;
        const SOCIAL_DESCRIPTION = 'socialDescription';
        const SOCIAL_TITLE = 'socialTitle';

        return {
            LINK,
            SOCIAL_TITLE,
            SOCIAL_DESCRIPTION
        }
    }
});
</script>

<style lang="scss" src="./social.scss" />
