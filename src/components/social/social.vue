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
        const LINK = 'prochorz.github.io/avatar-framer/';
        const SOCIAL_DESCRIPTION = 'Ваше молчание убивает людей 🇺🇦 и 🇷🇺. Любая огласка сейчас поможет прекратить это всё. Сделайте свой вклад в мир во всем мире!';
        const SOCIAL_TITLE = 'Молчание убивает';

        return {
            LINK,
            SOCIAL_TITLE,
            SOCIAL_DESCRIPTION
        }
    }
});
</script>

<style lang="scss" src="./social.scss" />
