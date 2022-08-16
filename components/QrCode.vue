<template>
    <div>
        <h3>Сканировать QR Code</h3>
        <el-button @click="scanIt" type="primary" round>Сканировать</el-button>
        <div class="reader-block reader-block--padding">
            <div id="reader" class="reader"></div>
        </div>
        <el-button v-if="scanCloseShow" @click="scanEnd" type="warning" round>Завершить</el-button>
        <el-input placeholder="Please input" v-model="qrCodeText"></el-input>
    </div>
</template>

<script>
    import {Html5QrcodeScanner} from "html5-qrcode";

    export default {
        name: 'QrCode',
        data() {
            return {
                scanCloseShow: false,
                qrCodeInputShow: false,
                qrCodeText: '',
                html5QrcodeScanner: {}
            }
        },
        methods: {
            myScanSuccess(decodedResult) {
                // Скрываем\показываем элементы, отображаем результат
                this.scanCloseShow = false;
                this.qrCodeInputShow = true;
                this.qrCodeText = decodedResult.decodedText;
            },
            scanIt() {
                let self = this;
                //Показываем кнопку Завершить
                this.scanCloseShow = true;
                //Скрываем инпут, на случай если нажали сканировать повторно 
                this.qrCodeInputShow = false;
                function onScanSuccess(decodedText, decodedResult) {
                    // handle the scanned code as you like, for example:
                    //console.log(`Code matched = ${decodedText}`, decodedResult);
                   
                    self.myScanSuccess(decodedResult);
                    
                    // Stop scanning
                    self.html5QrcodeScanner.clear().then(_ => {
                        // the UI should be cleared here 
                    }).catch(error => {
                        // Could not stop scanning for reasons specified in `error`.
                        // This conditions should ideally not happen.
                    });
                }

                function onScanFailure(error) {
                    // handle scan failure, usually better to ignore and keep scanning.
                    //console.warn(`Code scan error = ${error}`);
                }

                self.html5QrcodeScanner = new Html5QrcodeScanner(
                    "reader",
                    { 
                        fps: 10,
                        qrbox: {width: 250, height: 250},
                    },
                /* verbose= */ false
                );

                self.html5QrcodeScanner.render(onScanSuccess, onScanFailure);

            },
            scanEnd() {
                //Очищаем сканер
                this.html5QrcodeScanner.clear().then(_ => {
                        // the UI should be cleared here 
                }).catch(error => {
                    // Could not stop scanning for reasons specified in `error`.
                    // This conditions should ideally not happen.
                });
                //Скрываем кнопку "Завершить"
                this.scanCloseShow = false;
            }
        }
        
    }
</script>

<style>
    .reader {
        width: 600px;
    }
    .reader-block--padding {
        padding: 30px;
    }
</style>