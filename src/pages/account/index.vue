<template>
    <div class="account-wrapper">
        <div class="head">
            <sync-block class="sync-block"></sync-block>
            <go-net-btn class="net-btn"></go-net-btn>
        </div>
        <account-head class="item"></account-head>
        <div class="token-list item">
            <tokenCard v-for="token in tokenList" :key="token.id"
                       :opt="token" :sendTransaction="showTrans"></tokenCard>
        </div>
        <transaction v-if="isShowTrans" :token="activeToken" :closeTrans="closeTrans"></transaction>
    </div>
</template>

<script>
import syncBlock from 'components/syncBlock';
import goNetBtn from 'components/goNetBtn';
import accountHead from './head';
import tokenCard from './tokenCard';
import transaction from './transaction';

export default {
    components: {
        accountHead, syncBlock, tokenCard, transaction, goNetBtn
    },
    data() {
        return {
            isShowTrans: false,
            activeToken: null
        };
    },
    computed: {
        tokenList() {   // Force vite at first
            const tokenList = JSON.parse(JSON.stringify(this.$store.getters.tokenBalanceList));
            let viteTokenInfo = viteWallet.Ledger.getTokenInfo();
            if (!viteTokenInfo) {
                return tokenList;
            }

            const list = [];
            let viteId = viteTokenInfo.tokenId;
            if (tokenList[viteId]) {
                list.push(tokenList[viteId]);
                delete tokenList[viteId];
            }
            Object.keys(tokenList).forEach(k=>{
                list.push(tokenList[k]);
            });
            return list;
        }
    },
    methods: {
        showTrans(token) {
            if (!token.id) {
                return;
            }
            this.isShowTrans = true;
            this.activeToken = token;
        },
        closeTrans() {
            this.isShowTrans = false;
            this.activeToken = null;
        }
    }
};
</script>

<style lang="scss" rel="stylesheet/scss">
.account-wrapper {
    position: relative;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    overflow: auto;
    height: 100%;
    padding: 0 40px;
    .head {
        position: relative;
        text-align: center;
        margin-top: 20px;
        line-height: 40px;
    }
    .sync-block {
        display: inline-block;
    }
    .net-btn {
        position: absolute;
        right: 0px;
    }
}
.item {
    margin-top: 20px;
}
.token-list {
    display: flex;
    flex-wrap: wrap;
}

@media only screen and (max-width: 850px) {
    .account-wrapper .head {
        text-align: left;
    }
}

@media only screen and (max-width: 700px) {
    .account-wrapper {
        .sync-block {
            display: block;
        }
        .net-btn {
            position: relative;
            margin-top: 20px;
        }
    } 
}

@media only screen and (max-width: 550px) {
    .account-wrapper {
        padding: 0 15px;
    }
    .token-list {
        display: block;
    }
}
</style>
