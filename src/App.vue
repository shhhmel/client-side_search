<template>
    <div id="app">
        <div class="container mt-5">
            <div class="row">
                <div class="form-group col-md-6 mx-auto mb-5">
                    <label for="search">Search Crocodillians:</label>
                    <input name="search" v-model="query" class="form-control" />
                    <small class="form-text text-muted">
                        <strong>SUPER FAST</strong> search with hundreds of records and all sorts of data types.</small>
                </div>
                <div class="col-md-10 mx-auto">
                    <table class="table table-striped table-responsive">
                        <thead class="thead-inverse">
                            <th>#</th>
                            <th>Name</th>
                            <th>Age</th>
                            <th>Species</th>
                            <th>Country</th>
                            <th>Likes to Eat</th>
                        </thead>
                        <tbody>
                            <tr v-for="(result, index) of queryResults" :key="index">
                                <th scope="row">{{ index + 1 }}</th>
                                <td>{{ result.name }}</td>
                                <td>{{ result.age }}</td>
                                <td>{{ result.species }}</td>
                                <td>{{ result.country }}</td>
                                <td>{{ result.eats }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import fz from 'fuzzaldrin-plus';
import Crocodilians from './crocodilians.json';

export default {
    name: 'app',
    data: () => ({
        query: '',
        options: Crocodilians
    }),

    computed: {
        queryResults() {
            if (!this.query) return this.options;

            const preparedQuery = fz.prepareQuery(this.query);
            const scores = {};

            return this.options
                .map((option, index) => {
                    const scorableFields = [
                        option.uuid,
                        option.name,
                        option.species,
                        option.country,
                        option.eats,
                        `${option.age}`,
                    ].map(toScore => fz.score(toScore, this.query, { preparedQuery }));

                    scores[option.uuid] = Math.max(...scorableFields);

                    return option;
                })
                .filter(option => scores[option.uuid] > 1)
                .sort((a, b) => scores[b.uuid] - scores[a.uuid])
                ;
        }
    }
}
</script>