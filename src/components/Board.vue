<template>
    <div class="px-12 h-screen">
        <div class="grid h-full" :class="`grid-cols-${cols}`">
            <button v-for="block in blocks" :key="block.id" class="border-2 m-2 cursor-pointer rounded-md" :class="getClass(block)" @click="onClick(block)">
                <p class="font-black text-6xl">{{getDisplayValue(block)}}</p>
            </button>
        </div>
    </div>
</template>

<script>

const BLOCK_OPENED = 1
const BLOCK_CLOSED = 2
const BLOCK_DONE = 3

export default {
    name: 'Board',
    data() {
        return {
            blocks: [],
            prevBlock: null,
        }
    },
    props: {
        rows: Number,
        cols: Number
    },
    methods: {
        getDisplayValue(block) {
            if (block.state === BLOCK_OPENED) {
                return block.value
            }
            return '?'
        },

        isOpened(block) {
            return block.state === BLOCK_OPENED
        },

        isDone(block) {
            return block.state === BLOCK_DONE
        },

        openBlock(block) {
            block.state = BLOCK_OPENED
        },

        closeBlock(block) {
            block.state = BLOCK_CLOSED
        },

        quickCloseBlocksExcept(currentBlock) {
            this.blocks.filter(block => block.state === BLOCK_OPENED).filter(block => block.id !== currentBlock.id).forEach((openedBlock) => {
                this.closeBlock(openedBlock)
            })
        },

        markDone(block) {
            block.state = BLOCK_DONE
        },

        isGameOver() {
            var unsolvedBlocks = this.blocks.filter(block => block.state !== BLOCK_DONE)
            return unsolvedBlocks.length === 0
        },

        onClick(block) {
            
            if (this.isOpened(block) || this.isDone(block)) {
                console.log("NO OP")
                return
            }
            console.log("Opening...")
            this.openBlock(block)

            if (this.prevBlock) {
                if (block.value === this.prevBlock.value) {
                    console.log("Marking done")
                    setTimeout((oldBlock, newBlock) => {
                        this.markDone(oldBlock)
                        this.markDone(newBlock)    
                    }, 500, this.prevBlock, block);
                    
                } else {
                    setTimeout((oldBlock, newBlock) => {
                        this.closeBlock(oldBlock)
                        this.closeBlock(newBlock)
                    }, 1000, this.prevBlock, block);
                }
                this.prevBlock = null
            } else {
                this.quickCloseBlocksExcept(block)
                this.prevBlock = block
            }
        },

        getClass(block) {
            if (block.state === BLOCK_CLOSED) {
                return "bg-gray-300 text-gray-300 border-gray-500 hover:shadow-2xl"
            }
            else if (block.state == BLOCK_OPENED) {
                return "bg-yellow-300  cursor-not-allowed border-yellow-500" //hover:border-gray-500
            }
            else if (block.state == BLOCK_DONE) {
                return "bg-green-300 text-green-300  cursor-not-allowed border-green-500" 
            }
        }
    },

    created() {
        var possibleValues = []
        for (var value = 0; value < (this.rows * this.cols) / 2; value++) {
            possibleValues.push(value)
            possibleValues.push(value)
        }
        console.log(possibleValues)
        for (var index = 0; index < this.rows * this.cols; index++) {
            let randomValue = possibleValues.splice(Math.floor(Math.random()*possibleValues.length), 1)[0]
            this.blocks.push({id: index, value: randomValue, state: BLOCK_CLOSED})
        }
    }
}
</script>

<style>

</style>