<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-10">
                <div class="card">
                    <div class="card-header">Multiple Matrices</div>
                    <div class="card-body">
                        <div style="display: flex">
                            <matrix-component v-bind:matrix="matrix1"></matrix-component>
                            <matrix-component v-bind:matrix="matrix2"></matrix-component>
                        </div>
                        <hr/>
                        <div v-html="resultMatrix"></div>
                        <div v-if="this.errors.message !== ''" class="alert alert-danger">
                            <p v-text="this.errors.message"></p>
                            <ul>
                                <li v-for="error in this.errors.errors" v-text="error[0]"></li>
                            </ul>
                        </div>
                        <hr/>
                        <button @click="submit" class="btn btn-primary">Calculate</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import 'axios';
    import MatrixComponent from "./MatrixComponent";

    export default {
        components: {MatrixComponent},
        data() {
            return {
                matrix1: [
                    [1, 2, 3],
                    [4, 5, 6],
                ],
                matrix2: [
                    [7, 8],
                    [9, 10],
                    [11, 12],
                ],
                errors: {
                    message: '',
                    errors: [],
                },
                results: [],
                resultMatrix: ''
            }
        },
        methods: {
            submit: function () {
                this.resetData();

                const jsonData = {
                    'matrix1': this.matrix1,
                    'matrix2': this.matrix2,
                };
                axios.post('matrix/multiplication', jsonData)
                    .then(response => {
                        this.results = response['data']['total'];
                        this.mapResults();
                    }).catch(error => {
                    const response = error.response['data'];
                    this.errors.message = response['message'];
                    this.errors.errors = response['errors'];
                })
            },
            mapResults: function () {
                const columns = this.results.length;
                const rows = this.results[0].length;
                this.resultMatrix += '<h3>Results</h3>';
                for (let i = 0; i < columns; i++) {
                    this.resultMatrix += '<div class="matrix-column">';
                    for (let k = 0; k < rows; k++) {
                        this.resultMatrix += '<div>' + this.results[i][k] + '</div>';
                    }
                    this.resultMatrix += '</div>';
                }
            },
            resetData: function () {
                this.resultMatrix = '';
                this.errors.message = '';
                this.errors.errors = [];
            }
        }
    }
</script>

<style lang="scss">
    .matrix-column {
        display: flex;

        div {
            min-width: 50px;
            font-weight: bold;
            border: 1px solid #f1f1f1;
            text-align: center;
            padding: 10px;
            min-height: 44px;
        }
    }
</style>
