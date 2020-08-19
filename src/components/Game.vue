<template>
    <div class="game-wrapper">
        <h1>Simon Game</h1>
        <div class="game-field">
            <div ref="item1" class="field-item1" @click="checkSelectElement(1)"></div>
            <div ref="item2" class="field-item2" @click="checkSelectElement(2)"></div>
            <div ref="item3" class="field-item3" @click="checkSelectElement(3)"></div>
            <div ref="item4" class="field-item4" @click="checkSelectElement(4)"></div>
            <div class="disabled-field" v-if="isPlaySequence || !isStartGame"></div>
            <button v-if="!isStartGame" @click="startGame">Start</button>
            <button v-if="isStartGame" :disabled="isPlaySequence" @click="stopGame">Stop</button>
        </div>
        <div>
            <audio ref="audio1" src="../assets/sounds/1.mp3"></audio>
            <audio ref="audio2" src="../assets/sounds/2.mp3"></audio>
            <audio ref="audio3" src="../assets/sounds/3.mp3"></audio>
            <audio ref="audio4" src="../assets/sounds/4.mp3"></audio>
        </div>
        <div class="settings">
            <h3>Уровень сложности</h3>
            <div class="complexity-level">
                <input name="level" id="easy" type="radio" value=1500 v-model="level" :disabled="isStartGame">
                <label for="easy">Легкий</label><br>
                <input name="level" id="medium" type="radio" value=1000 v-model="level" :disabled="isStartGame">
                <label for="medium">Средний</label><br>
                <input name="level" id="hard" type="radio" value=400 v-model="level" :disabled="isStartGame">
                <label for="hard">Сложный</label>
            </div>
            <h3 v-model="round">Round {{round}}</h3>
            <span class="message" v-if="endGame" v-model="round">Вы проиграли на {{round}} раунде!</span>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Game",
        data() {
            return {
                level: 1500,
                sequence: [],
                playerSequence: [],
                round: 0,
                currentIndex: 0,
                isStartGame: false,
                isPlaySequence: false,
                endGame: false
            }
        },
        methods: {
            startGame() {
                if (!this.isStartGame) {
                    this.round = 0
                }
                this.isStartGame = true
                this.isPlaySequence = true
                this.currentIndex = 0
                this.round += 1
                this.addElementToSequence()
                this.endGame = false
                let index = 0
                let sequence = this.sequence
                let playSequence = this.playSequence
                let interval = setInterval(function () {
                    if (sequence.length - 1 >= index) {
                        playSequence(index)
                        index++
                    } else {
                        clearInterval(interval)
                    }
                }, this.level)
            },
            addElementToSequence() {
                this.sequence.push(Math.floor(Math.random() * 4) + 1)
            },
            // воспроизведение последовательности
            playSequence(index) {
                if (index) {
                    let previousElement = this.$refs["item" + this.sequence[index - 1]]
                    previousElement.classList.remove("active")
                }
                let currentElement = this.$refs["item" + this.sequence[index]]
                let sound = this.$refs["audio" + this.sequence[index]]
                currentElement.classList.add("active")
                setTimeout(() => currentElement.classList.remove("active"), 200)
                this.playSound(sound)
                if (this.sequence.length - 1 === index) {
                    setTimeout(() => currentElement.classList.remove("active"), this.level)
                    this.isPlaySequence = false
                }
            },
            // проверка выбираемых игроком кнопок
            checkSelectElement(element) {
                if (this.isStartGame) {
                    this.playerSequence.push(element)
                    let sound = this.$refs["audio" + [element]]
                    this.playSound(sound)
                    if (this.playerSequence[this.currentIndex] === this.sequence[this.currentIndex]) {
                        this.currentIndex += 1
                        if (this.playerSequence.length === this.sequence.length) {
                            this.playerSequence = []
                            setTimeout(this.startGame, 500)
                        }
                    } else {
                        this.stopGame()
                    }
                }
            },
            playSound(sound){
                sound.pause()
                sound.currentTime = 0
                sound.play()
            },
            stopGame() {
                this.endGame = true
                this.sequence = []
                this.playerSequence = []
                this.isStartGame = false
            }
        }
    }
</script>

<style lang="scss" scoped>

    .game-wrapper {
        margin: 0 auto;
        width: 800px;
        font-family: 'Roboto', sans-serif;

        h1 {
            text-align: center;
            margin-bottom: 50px;
        }
    }

    .settings {
        float: left;
        margin-left: 100px;

        .message {
            color: #136a0f;
        }
    }

    .game-field {
        width: 400px;
        height: 400px;
        border: 2px solid gray;
        position: relative;
        border-radius: 50%;
        overflow: hidden;
        float: left;

        button {
            position: absolute;
            width: 100px;
            height: 100px;
            border-radius: 50%;
            z-index: 1;
            top: 50%;
            left: 50%;
            margin: -50px 0 0 -50px;
            border: 2px solid gray;
            background-color: #fff;
            cursor: pointer;
            outline: none;
            transition: background-color 0.1s;
            &:hover {
                background-color: #F7F7F7;
            }
        }

        .disabled-field {
            width: 400px;
            height: 400px;
            position: absolute;
            top: 0;
            left: 0;
        }

        .field-item1, .field-item2, .field-item3, .field-item4 {
            width: 200px;
            height: 200px;
            float: left;
        }

        .field-item1 {
            background-color: #679BED;
            transition: background-color 0.1s;
            &:hover {
                cursor: pointer;
                background-color: #5376ED;
            }

            &:active, &.active {
                background-color: #2f3aed;
            }
        }

        .field-item2 {
            background-color: #ed5f60;
            transition: background-color 0.1s;
            &:hover {
                cursor: pointer;
                background-color: #d64c59;
            }

            &:active, &.active {
                background-color: #ed0424;
            }
        }

        .field-item3 {
            background-color: #E6ED64;
            transition: background-color 0.1s;
            &:hover {
                cursor: pointer;
                background-color: #d6d53e;
            }

            &:active, &.active {
                background-color: #edc200;
            }
        }

        .field-item4 {
            background-color: #8bdf6a;
            transition: background-color 0.1s;
            &:hover {
                cursor: pointer;
                background-color: #48bd4a;
            }

            &:active, &.active {
                background-color: rgba(22, 162, 18, 0.89);
            }
        }
    }
</style>