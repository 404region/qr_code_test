<template>
    <div>
        <h3>Сканировать QR Code</h3>
        <el-button @click="scanIt" type="primary" round>Сканировать</el-button>
         <el-input placeholder="Здесь будет ваш QR Code" v-model="qrCodeText" class="qrcode-input"></el-input>
        <transition appear>
            <div class="modal-overlay" v-if="showModal">
                <div class="modal">
                    <div class="modal__inner-block">
                        <div class="reader-block reader-block--padding">
                            <div id="reader" class="reader"></div>
                        </div>
                        <el-button @click="scanEnd" type="warning" round>Завершить</el-button>
                    </div>
                </div>
            </div>
       </transition>
    </div>
</template>
<script>
    import {Html5Qrcode} from "html5-qrcode";


    export default {
        name: 'MyQrCode',
        data() {
            return {
                qrCodeText: '',
                html5QrcodeScanner: {},
                showModal: false,
            }
        },
        methods: {
            myScanSuccess(decodedResult) {
                // Скрываем\показываем элементы, отображаем результат
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
                });*/
            },
            scanIt() {
                const vueObj = this;
                vueObj.showModal = true;
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
    .qrcode-input {
        padding: 26px 0;
    }
    .modal-overlay {
        position: absolute;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        z-index:98;
        background-color: rgba(0, 0, 0, 0.3);
        display: flex;
        justify-content: center;
        align-items: center;
    }
    .modal__inner-block {
        background-color: #fff;
        border-radius: 20px;
        border: 1px solid gray;
        padding: 20px;
    }
</style>