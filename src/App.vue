<template>
    <div id="app" class="container">
        <ApolloQuery :query="require('@/graphql/queries/FilteredProducts.graphql')" :variables="{
            resolution: selectedResolution,
            display: selectedDisplay,
            processor: selectedProcessor,
            memory: selectedMemory,
            internal_memory: selectedInternalMemory,
            graphics_card: selectedGraphicsCard,
            page: page}">

            <template slot-scope="{ result: { data, loading }, isLoading }">
                <div class="row">
                    <div>
                        <form class="form-check mt-2 mt-md-0">
                            <div class="form-group">
                                <select class="browser-default custom-select "
                                        v-model="selectedDisplay"
                                        @change="page=1;">
                                    <option value="not_specified">Дисплеј</option>
                                    <option value="11.6">11.6</option>
                                    <option value="13.3">13.3</option>
                                    <option value="13.9">13.9</option>
                                    <option value="14">14</option>
                                    <option value="14.1">14.1</option>
                                    <option value="15.6">15.6</option>
                                    <option value="16.0">16</option>
                                    <option value="17.3">17.3</option>
                                    <option value="23.8">23.8</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <select class="browser-default custom-select "
                                        v-model="selectedInternalMemory"
                                        @change="page=1;">
                                    <option value="not_specified">Внатрешна меморија</option>
                                    <option value="4">4GB</option>
                                    <option value="8">8GB</option>
                                    <option value="12">12GB</option>
                                    <option value="16">16GB</option>
                                    <option value="32">32GB</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <select class="browser-default custom-select "
                                        v-model="selectedMemory"
                                        @change="page=1;">
                                    <option value="not_specified">Меморија</option>
                                    <option value="32">32GB eMMC</option>
                                    <option value="64">64GB eMMC</option>
                                    <option value="128">128GB SSD</option>
                                    <option value="256">256GB SSD</option>
                                    <option value="500">500GB HDD</option>
                                    <option value="512">512GB SSD</option>
                                    <option value="1TB">1TB HDD or SSD</option>
                                    <option value="2TB">2TB HDD</option>
                                    <option value="Two">2 hard drives</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <select class="browser-default custom-select "
                                        v-model="selectedResolution"
                                        @change="page=1;">
                                    <option value="not_specified">Резолуција</option>
                                    <option value="1140">1140x900</option>
                                    <option value="1360">1360x768</option>
                                    <option value="1366">1366x768</option>
                                    <option value="1920">1920x1080</option>
                                    <option value="2560">2560x1600</option>
                                    <option value="3072">3072x1920</option>
                                    <option value="3840">3840x2160</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <select class="browser-default custom-select "
                                        v-model="selectedProcessor"
                                        @change="page=1;">
                                    <option value="not_specified">Процесор</option>
                                    <option value="pentium">Intel Pentium</option>
                                    <option value="celeron">Intel Celeron</option>
                                    <option value="atom">Intel Atom</option>
                                    <option value="i3">Intel Core i3</option>
                                    <option value="i5">Intel Core i5</option>
                                    <option value="i7">Intel Core i7</option>
                                    <option value="amd">AMD</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <select class="browser-default custom-select "
                                        v-model="selectedGraphicsCard"
                                        @change="page=1;">
                                    <option value="not_specified">Графичка картичка</option>
                                    <option value="Integrated">Integrated</option>
                                    <option value="Dedicated">Dedicated</option>
                                </select>
                            </div>
                        </form>
                        <div style="text-align: left;" class="form-check">
                            <button @click="previous()" class="btn btn-info m-1" :disabled="page===1">Назад</button>
                            <button @click="next()" class="btn btn-info">Вчитај уште</button>
                        </div>
                    </div>

                    <!-- loading --->
                    <div v-if="isLoading" style="width: 60%; align-self: center">
                        <font-awesome-icon icon="spinner" class="fa fa-spin" style="height: 30px; width: 20px;"/>
                    </div>

                    <!-- there is data loaded --->
                    <div v-else-if="data.filter.data.length > 0" v-for="product of data.filter.data" :key="product.id"
                         class="product">
                        <div class="card">
                            <div class="image-container">
                                <img class="card-img-top" :src="product.image_src"
                                     style='width: 228px; height: 228px; object-fit: scale-down;"'
                                     :alt="product.name">
                            </div>
                        </div>
                        <div class="card-body">
                            <div class="row">
                                <ul class="list-group list-group-flush">
                                    <li class="list-group-item image-container">
                                        <img v-if="product.url.includes('setec')" src="./assets/setec-logo.png"
                                             :alt="product.name" style='width: 228px; margin: 23px 0;'>
                                        <img v-else src="./assets/neptun_logo.png" :alt="product.name"
                                             style='width: 228px'>
                                    </li>
                                    <li class="list-group-item  align-content-center">
                                        <h5 class="card-title" style="font-size: 1.07rem; min-height: 60px">
                                            <a :href="product.url">{{ product.name }}</a><br/>
                                        </h5>
                                    </li>
                                    <li class="list-group-item">Цена со попуст: {{ product.club_price }} ден.</li>
                                    <li class="list-group-item font-weight-bold">Редовна цена: {{ product.price }} ден.
                                    </li>
                                </ul>
                            </div>
                        </div>
                    </div>

                    <!-- last page  -->
                    <div v-else class="row" style="margin-left: 20%; align-self: center">
                        <p class="text-info text-center">
                            <font-awesome-icon icon="minus-circle" class="fa"/>
                            <br/>
                            Нема повеќе продукти со избраните спецификации!
                        </p>
                    </div>
                </div>
            </template>
        </ApolloQuery>
    </div>
</template>

<script>

export default {
    name: 'App',
    components: {},
    data() {
        return {
            page: 1,
            total: 200,
            selectedResolution: "not_specified",
            selectedDisplay: "not_specified",
            selectedProcessor: "not_specified",
            selectedMemory: "not_specified",
            selectedInternalMemory: "not_specified",
            selectedGraphicsCard: "not_specified",
            products: []
        }
    }, methods: {
        previous() {
            if (this.page > 1) {
                this.page--;
            }
        },
        next() {
            if (this.page * 3 <= this.total) {
                this.page++;
            }
        }
    }

}

</script>

<style>

#app {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    text-align: center;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    margin-top: 60px;
    background: #eee;
}

body {
    background: #eee !important;
}

h1 {
    margin-top: 3rem;
    margin-bottom: 2rem;
    text-align: center;
}

h5 small {
    color: #aaa;
}

.product {
    border: 1px solid #ccc;
    border-radius: 7px;
    background-color: #fff;
    margin: 1rem;
    margin-top: 0;
    width: 265px;
}

.product:hover {
    -webkit-box-shadow: 0 13px 21px -5px rgba(0, 0, 0, .2);
    box-shadow: 0 13px 21px -5px rgba(0, 0, 0, .2);
}

a {
    font-size: 1rem;
    color: black;
}

a:hover {
    text-decoration: none;
    color: #44a28a;
}

.image-container {
    display: flex;
    justify-content: center;
}

.card-img-top {
    height: 110px;
}

.list-group-item + .list-group-item {
    cursor: default;
}

.list-group-item {
    padding: 0.25rem 0 !important;
}

</style>
