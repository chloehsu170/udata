<style lang="less">
.row.user-card-filter-search {
    margin-bottom: 20px;
}
</style>

<template>
<div>
<div class="row user-card-filter-search">
    <div class="col-xs-12 col-md-6 col-md-offset-3">
        <form class="search-form">
            <div class="input-group">
                <input type="text" name="search" class="form-control"
                    :placeholder="_('Search')" v-model="search_query" />
                <div class="input-group-btn">
                    <button type="submit" name="submit" class="btn btn-warning btn-flat">
                        <span class="fa fa-search"></span>
                    </button>
                </div>
            </div>
        </form>
    </div>
</div>
<div class="card-list card-list--columned user-card-filter-cardlist" v-if="completions">
    <div :class="cardclass" v-for="user in users" :key="user.id">
        <user-card :user="user" clickable></user-card>
    </div>
</div>
<div class="row" v-if="!search_query">
    <p class="col-xs-12 lead text-center">
    {{ _('Start typing to find your user.') }}
    </p>
</div>
<div class="row" v-if="search_query && !users.length">
    <p class="col-xs-12 lead text-center">
    {{ _('No user found.') }}
    </p>
</div>
</div>
</template>

<script>
import API from 'api';
import log from 'logger';
import User from 'models/user';
import UserCard from 'components/user/card.vue';

export default {
    components: {UserCard},
    props: {
        cardclass: {
            type: String,
            default: 'col-xs-12 col-md-4 col-lg-3'
        }
    },
    data() {
        return {
            search_query: '',
            completions: []
        };
    },
    computed: {
        users() {
            return this.completions.map(user => new User({data: {
                id: user.id,
                avatar: user.avatar_url,
                first_name: user.first_name,
                last_name: user.last_name,
                page: `/user/${user.slug}/`
            }}));
        }
    },
    watch: {
        search_query(query) {
            API.users.suggest_users({
                q: query,
                size: 9
            }, (data) => {
                this.completions = data.obj;
            }, function(message) {
                log.error('Unable to fetch users', message);
            });
        }
    }
};
</script>
