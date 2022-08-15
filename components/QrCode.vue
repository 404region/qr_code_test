<template>
    <div>
        <h3>Сканировать QR Code</h3>
        <el-button @click="scanIt" type="primary" round>Сканировать</el-button>
        <div class="reader-block reader-block--padding">
            <div id="reader" class="reader"></div>
        </div>
        <el-button v-if="scanOpen" @click="scanEnd" type="warning" round>Завершить</el-button>
        <el-input v-if="qrCodeInputShow" placeholder="Please input" v-model="qrCodeText"></el-input>
    </div>
</template>

<script>
    import {Html5QrcodeScanner} from "html5-qrcode";

    export default {
        name: 'QrCode',
        data() {
            return {
                scanOpen: false,
                qrCodeInputShow: false,
                qrCodeText: '',
            }
        },
        methods: {
            myScanSuccess(decodedResult) {
                console.log('myScanSuccess', decodedResult);
                this.scanOpen = false;
                this.qrCodeInputShow = true;
                this.qrCodeText = decodedResult.decodedText;
            },
            scanIt() {
                let self = this;
                this.scanOpen = true;
                this.qrCodeInputShow = false;
                console.log('scan');
                function onScanSuccess(decodedText, decodedResult) {
                    // handle the scanned code as you like, for example:
                    console.log(`Code matched = ${decodedText}`, decodedResult);
                    console.log('еще консоль');
                    console.log('еще консоль 2 ');
                    console.log('self', self);
                   
                    self.myScanSuccess(decodedResult);
                    //console.log('his.scanOpen',this.scanOpen);
                    
                    // Stop scanning
                    html5QrcodeScanner.clear().then(_ => {
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

                let html5QrcodeScanner = new Html5QrcodeScanner(
                    "reader",
                    { fps: 10, qrbox: {width: 250, height: 250} },
                /* verbose= */ false
                );

                html5QrcodeScanner.render(onScanSuccess, onScanFailure);

            },
            scanEnd() {
                console.log('scan end');
                //Пишем в инпут нужный текст
                //decodedText


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