<style>
    .vue-header a {
        color: inherit;
    }

    .vue-header .dev-version {
        color: #2a88ff;
        left: -15px;
        padding: 5px;
        position: relative;
        top: 10px;
    }

    .vue-header .btn-group-toggle {
        margin-left: 20px;
    }

    .vue-header .btn-group-toggle label {
        padding: 0 10px;
    }

    @media (min-width: 992px) {
        .vue-header .navbar-nav>li>a {
            border-bottom: 2px solid transparent;
        }

        .vue-header .navbar-nav>li>a:hover,
        .vue-header .navbar-nav>li.active>a,
        .vue-header .navbar-nav>li.show>a {
            border-bottom-color: #3498db;
        }

        .vue-header.navbar.navbar-default .navbar-nav>li>a {
            background-color: initial;
        }
    }

    @media (max-width: 991px) {
        .vue-header .form-inline {
            align-items: center;
            display: flex;
        }

        .vue-header .form-inline input {
            flex: 1;
            margin: 0 10px 0;
        }
    }
</style>
<template>
    <nav class="bg-light navbar navbar-expand-lg navbar-light vue-header">
        <div class=container>
            <div>
                <router-link to=/ class=navbar-brand>
                    <img src=/static/img/logo.png width=150 alt="">
                </router-link>
                <a href=https://github.com/nebulasio/explorer/issues target=_blank class=dev-version data-toggle=tooltip data-placement=bottom data-html=true title='
<span class="fa fa-flask" aria-hidden=true></span>
<span class=c777>This website is under heavy construction</span><br>
<div>Feel free to submit issues by clicking this link 👍</div>
                '>alpha</a>
            </div>

            <button class=navbar-toggler type=button data-toggle=collapse data-target=#navbarSupportedContent aria-controls=navbarSupportedContent aria-expanded=false aria-label="Toggle navigation">
                <span class=navbar-toggler-icon></span>
            </button>
            <div class="collapse navbar-collapse" id=navbarSupportedContent>
                <ul class="navbar-nav mr-auto">
                    <li class=nav-item v-bind:class="{ active: $route.meta.headerActive == 1 }">
                        <router-link to=/ class=nav-link>HOME
                            <span class=sr-only>(current)</span>
                        </router-link>
                    </li>
                    <li class="dropdown nav-item" v-bind:class="{ active: $route.meta.headerActive == 2 }">
                        <a class="nav-link dropdown-toggle" href=# id=header-dropdown-blockchain role=button data-toggle=dropdown aria-haspopup=true aria-expanded=false>BLOCKCHAIN</a>
                        <div class=dropdown-menu aria-labelledby=header-dropdown-blockchain>
                            <router-link class=dropdown-item to=/txs>View Txns</router-link>
                            <router-link class=dropdown-item to=/txs/pending>View Pending Txns</router-link>
                            <router-link class=dropdown-item to=/blocks>View Blocks</router-link>
                        </div>
                    </li>
                    <li class=nav-item v-bind:class="{ active: $route.meta.headerActive == 3 }">
                        <router-link class=nav-link to=/accounts>ACCOUNT</router-link>
                    </li>
                    <li class="dropdown nav-item">
                        <a class="nav-link dropdown-toggle" href=# id=header-dropdown-misc role=button data-toggle=dropdown aria-haspopup=true aria-expanded=false>MISC</a>
                        <div class=dropdown-menu aria-labelledby=header-dropdown-misc>
                            <!-- 先不显示这一条 <a class="nav-link" href=# v-on:click="apiSwitch('main')">
                                <span class="fa fa-check" v-bind:style="{ visibility: apiType == 'main' ? '' : 'hidden' }" aria-hidden=true></span>
                                Mainnet
                            </a> -->
                            <a class=nav-link href=# v-on:click="apiSwitch('test')">
                                <span class="fa fa-check" v-bind:style="{ visibility: apiType == 'test' ? '' : 'hidden' }" aria-hidden=true></span>
                                Testnet
                            </a>
                        </div>
                    </li>
                    <!-- <li class=nav-item>
                        <a class=nav-link href=#>CHART
                            <span class=sr-only>(current)</span>
                        </a>
                    </li>
                    <li class=nav-item>
                        <a class="nav-link disabled" href="#">Disabled</a>
                    </li> -->
                </ul>
                <form class=form-inline v-on:submit.prevent=onSubmit>
                    <input class="form-control mr-sm-2" v-model=search type=search placeholder=Search>
                    <button class="btn btn-outline-success" type=submit>GO</button>
                </form>
            </div>
        </div>
    </nav>
</template>
<script>
    var api = require("@/assets/api");

    module.exports = {
        data() {
            return {
                apiType: "",
                search: ""
            };
        },
        methods: {
            apiSwitch(apiType) {
                if (sessionStorage.apiType != apiType) {
                    sessionStorage.apiType = apiType;
                    location.reload();
                }
            },
            onSubmit() {
                this.$root.showModalLoading = true;

                api.getSearch(this.search, o => {
                    this.$root.showModalLoading = false;
                    this.search = "";

                    if (o.type == "block")
                        this.$router.push("/block/" + o.q);
                    else if (o.type == "address")
                        this.$router.push("/address/" + o.q);
                    else if (o.type == "transaction")
                        this.$router.push("/tx/" + o.q);
                    else {
                        this.$root.search = o.q;
                        this.$router.push("/oops");
                    }
                }, () => {
                    this.$root.search = this.search;
                    this.$root.showModalLoading = false;
                    this.search = "";
                    this.$router.push("/oops");
                });
            }
        },
        mounted() {
            require("jquery")("[data-toggle=tooltip]").tooltip();

            if (sessionStorage.apiType != "main" && sessionStorage.apiType != "test")
                sessionStorage.apiType = this.apiType = "test";
            else
                this.apiType = sessionStorage.apiType;
        }
    };
</script>