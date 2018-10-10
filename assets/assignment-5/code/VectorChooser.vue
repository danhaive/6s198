<!-- Contributed by David Bau, in the public domain -->

<template>
    <div class="vectorlist">
        <div v-for="(vector, index) in vectors" class="vector">
            <input v-model="vector.text">
            <button @click="selectVector(index)">&rarr;</button>
            <button @click="deleteVector(index)">x</button>
        </div>
        <div class="operation">
            <button @click="saveVector()">Save current sample</button>
        </div>
        <div class="operation">
            <!-- TODO: Change this button to do something interesting -->
            <button @click="applyVectorMath()">Apply vector math</button>
        </div>
        <div class="operation">
            <!-- TODO: Change this button to do something interesting -->
            <button @click="getKNN()">Get Nearest Font</button>
        </div>
        <div class="operation">
            <!-- TODO: Change this button to do something interesting -->
            <button @click="getAverageNN()">Get Average Font</button>
        </div>
        <!--<div class="operation">-->
        <!--&lt;!&ndash; TODO: Change this button to do something interesting &ndash;&gt;-->
        <!--<button @click="getAverageNN()">Get Nearest Average Font</button>-->
        <!--</div>-->
        <!-- TODO: Add the KNN font ID button below -->
    </div>
</template>

<script>
    import {Array1D, ENV, Scalar, Array2D} from 'deeplearn';

    const math = ENV.math;
    const BATCH_SIZE = 10000;
    const NLOGITS = 40;


    //This json file includes all of the Font IDs in our database and their 40-dimensional logits vector.
    var json = require('../embeddings.json');

    // helper functions
    function computeDistance(arr1, arr2) {
        var norm1 = math.norm(arr1)
        var norm2 = math.norm(arr2)
        var dp = math.dotProduct(arr1, arr2)
        var normalization = norm1.getValues() * norm2.getValues()
        normalization = (normalization < 1E-16) ? 1 : normalization
        var result = dp.getValues() / normalization
        return result
    };

    function closestFont(generatedFont) {
        // SLOW > to modify to tensor calcs
        var arr = Object.values(json);
        var max_similarity_index = 0;
        var max_similarity = computeDistance(Array1D.new(arr[0]), generatedFont)
        for (var i = 1; i < arr.length; i++) {
            var font = Array1D.new(arr[i])
            var similarity = computeDistance(font, generatedFont)
            if (similarity > max_similarity) {
                max_similarity = similarity;
                max_similarity_index = i
            }
            console.log("Font ID: " + String(i) + " | Similarity: " + String(similarity.toFixed(3)))
        }
        console.log("ID of Closet Font: " + String(max_similarity_index))
    }

    export default {
        props: {
            selectedSample: {},
            model: {},
            vectors: {type: Array, default: () => [{text: "0"}]}
        },
        methods: {
            saveVector() {
                this.selectedSample.data().then(x =>
                    this.vectors.push({text: Array.prototype.slice.call(x).join(',')})
                );
            },
            deleteVector(index) {
                this.vectors.splice(index, 1);
            },
            selectVector(index) {
                this.$emit("select", {
                    selectedSample: this.model.fixdim(
                        Array1D.new(this.vectors[index].text.split(',').map(parseFloat)))
                });
            },
            // TODO: Add useful vector space operations here -->
            applyVectorMath() {
                var ids_add = [5757, 5687, 4934, 7631]
                var ids_substract = [7933, 3367, 3718, 888]
                var arr = Object.values(json)
                var font_vector = Array1D.zeros([NLOGITS])
                for (var id in ids_add) {
                    font_vector = math.add(font_vector, Array1D.new(arr[ids_add[id]]))
                    font_vector = math.subtract(font_vector, Array1D.new(arr[ids_substract[id]]))
                }
                font_vector = math.arrayDividedByScalar(font_vector, Scalar.new(ids_add.length))
                this.$emit("select", {
                    selectedSample:
                        math.add(this.selectedSample, this.model.fixdim(
                            font_vector))
                    //Array1D.new([0.06224909797310829, -0.03927520662546158, 0.036304060369729996, 0.08979060500860214, 0.07010388374328613, -0.053155433386564255, 0.0506797656416893, -0.2114126980304718, 0.029749730601906776, -0.11237722635269165, 0.0035086243879050016, 0.08917944878339767, 0.023440062999725342, -0.12403910607099533, -0.0113179637119174, 0.04081672057509422, -0.09625156223773956, 0.04357428103685379, -0.024022696539759636, -0.13571149110794067, -0.07631946355104446, -0.1691514551639557, 0.022216998040676117, -0.02769692800939083, 0.19982090592384338, -0.030413871631026268, 0.03911220654845238, -0.00327073666267097, -0.1532777100801468, 0.06465577334165573, 0.053830768913030624, -0.06032722070813179, 0.13749805092811584, -0.09812066704034805, 0.049291905015707016, 0.11244677752256393, -0.05859237536787987, -0.04098224267363548, 0.006077930796891451, 0.027828747406601906])))
                })
            },


            //TODO: Implement getKNN to output the font ID of the nearest neighbor
            getKNN() {
                closestFont(this.selectedSample)
            },

            getAverageNN() {
                var arr = Object.values(json)
                var length = arr.length
                console.log(arr.length)
                //COMPUTE AVERAGE FONT IN BATCHES
                var i = 0;
                var avg_font = Array1D.zeros([40])
                while (i < length) {
                    var subarr = (i + 10000 < arr.length) ? arr.slice(i, i + BATCH_SIZE) : arr.slice(i)
                    var nfonts = subarr.length
                    var batch_fonts = Array2D.new([nfonts, NLOGITS], subarr)
                    var avg_subarr = math.sum(batch_fonts, 0)
                    avg_subarr = math.arrayDividedByScalar(avg_subarr, Scalar.new(nfonts))
                    console.log(avg_subarr.shape)
                    avg_font = math.add(avg_font, avg_subarr)
                    i = i + BATCH_SIZE
                }
                closestFont(avg_font)
            }
        },
        watch: {
            model: function (val) {
                for (let i = 0; i < this.vectors.length; ++i) {
                    let arr = this.vectors[i].text.split(',');
                    if (arr.length > this.model.dimensions) {
                        arr = arr.slice(0, this.model.dimensions);
                    }
                    while (arr.length < this.model.dimensions) {
                        arr.push('0');
                    }
                    this.vectors[i].text = arr.join(',');
                }
            }
        }
        ,
    }


</script>

<style scoped>
    .vector, .operation {
        border-top: 1px solid rgba(0, 0, 0, 0.1);
        white-space: nowrap;
    }

</style>
