<template>
    <div class="main__step main__step--3 main__step main__step--social">
        <div class="main__step___message">
            <span class="main__step__count">3</span>
            <div class="main__step__text"></div>
        </div>
        <div class="main__step__input">
            Поделитесь в соц сетях и создайте мир над головами
            <ShareNetwork
                network="vk"
                :url="LINK"
                :title="SOCIAL_TITLE"
                :description="SOCIAL_DESCRIPTION"
            >
                Share on vk
            </ShareNetwork>
            <ShareNetwork
                network="facebook"
                :url="LINK"
                :title="SOCIAL_TITLE"
                :description="SOCIAL_DESCRIPTION"
            >
                Share on Facebook
            </ShareNetwork>
            <ShareNetwork
                network="odnoklassniki"
                :url="LINK"
                :title="SOCIAL_TITLE"
                :description="SOCIAL_DESCRIPTION"
            >
                Share on odnoklassniki
            </ShareNetwork>
        </div>
    </div>
</template>

<script>
import { defineComponent } from 'vue';


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
