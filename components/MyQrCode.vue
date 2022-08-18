<template>
    <div>
        <h3>Сканировать QR Code</h3>
        <el-button @click="scanIt" type="primary" round>Сканировать</el-button>
        <div class="reader-block reader-block--padding">
            <div id="reader" class="reader"></div>
        </div>
        <el-button v-if="scanCloseShow" @click="scanEnd" type="warning" round>Завершить</el-button>
        <el-input placeholder="Здесь будет ваш QR Code" v-model="qrCodeText"></el-input>
    </div>
</template>
<script>
    import {Html5Qrcode} from "html5-qrcode";

    export default {
        name: 'MyQrCode',
        data() {
            return {
                scanCloseShow: false,
                qrCodeText: '',
                html5QrcodeScanner: {} ,
            }
        },
        methods: {
            myScanSuccess(decodedResult) {
                // Скрываем\показываем элементы, отображаем результат
                //this.scanCloseShow = false;
                console.log(this);
                this.qrCodeText = decodedResult.decodedText;
            },
            scanEnd() {
                //Очищаем сканер
                console.log('end scanner')
                /* this.html5QrcodeScanner.clear().then(_ => {
                        // the UI should be cleared here 
                }).catch(error => {
                    // Could not stop scanning for reasons specified in `error`.
                    // This conditions should ideally not happen.
                });
                //Скрываем кнопку "Завершить"
                this.scanCloseShow = false;*/
            },
            scanIt() {
                const vueObj = this;
                 console.log('start scan');
                Html5Qrcode.getCameras().then(devices => {
                    /** 
                     * devices would be an array of objects of type: 
                     * { id: "id", label: "label" }
                     */
                    if (devices && devices.length) {
                        console.log('devices', devices);
                        vueObj.cameraId = devices[0].id; 
                        // .. use this to start scanning.
                        vueObj.html5QrCode = new Html5Qrcode(/* element id */ "reader");
                        vueObj.html5QrCode.start(
                            vueObj.cameraId, 
                            {
                                fps: 10,    // Optional, frame per seconds for qr code scanning
                                qrbox: { width: 250, height: 250 }  // Optional, if you want bounded box UI
                            },
                            (decodedText, decodedResult) => {
                                // do something when code is read
                                vueObj.myScanSuccess(decodedResult);
                                vueObj.scanEnd();
                            },
                            (errorMessage) => {
                                // parse error, ignore it.
                            }
                        )
                        .catch((err) => {
                        // Start failed, handle it.
                        });
                            }
                        }).catch(err => { 
                            // handle err
                        });

                


                 console.log('end scan');
            }

            
        }
        
    }
</script>

<style>

</style>