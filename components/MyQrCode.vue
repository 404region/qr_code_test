<template>
    <div>
        <h3>Сканировать QR Code</h3>
        <el-button @click="scanIt" type="primary" round>Сканировать</el-button>
         <el-input placeholder="Здесь будет ваш QR Code" v-model="qrCodeText" class="qrcode-input"></el-input>
        <transition appear>
            <div class="modal-overlay" v-show="showModal">
                <div class="modal">
                    <div class="modal__inner-block" v-loading="loading">
                        <div class="reader-block reader-block--padding">
                            <div id="reader" class="reader"></div>
                        </div>
                        <el-button @click="scanEnd" type="warning" round v-show="showCloseBtn">Завершить</el-button>
                    </div>
                </div>
            </div>
       </transition>
    </div>
</template>
<script lang="ts">
    import { Html5Qrcode } from "html5-qrcode";
    import { Html5QrcodeResult } from "html5-qrcode/esm/core";
    import { Component, Vue } from "nuxt-property-decorator";

    @Component({
        components: {  },
    })
    export default class MyQrCode extends Vue {
        qrCodeText:string = '';
        html5Qrcode:Html5Qrcode | null = null;
        showModal:boolean = false;
        cameraId:string = '';
        loading:boolean = false;
        showCloseBtn:boolean = false;
        
        openAlertNoCameraPermission() {
            this.$alert('Для сканирования Qr-Code необходимо предоставить доступ к камере', 'Ошибка', {
            confirmButtonText: 'OK'
            });
        }

        myScanSuccess(decodedResult: Html5QrcodeResult) {
            // Скрываем\показываем элементы, отображаем результат
            this.qrCodeText = decodedResult.decodedText;
        }

        scanEnd() {
            this.showModal = false;
            
            try {
                (this.html5Qrcode)!.stop().then((ignore) => {
                // QR Code scanning is stopped.
                }).catch((err) => {
                // Stop failed, handle it.
                });
            } catch (error) {
                
            }

        }

        scanIt() {
            this.showModal = true;
            this.loading = true;
            this.showCloseBtn = false;

            Html5Qrcode.getCameras().then(devices => {
                /** 
                 * devices would be an array of objects of type: 
                 * { id: "id", label: "label" }
                 */
                if (devices && devices.length) {
                    this.cameraId = devices[0].id; 
                    // .. use this to start scanning.
                    this.html5Qrcode = new Html5Qrcode(/* element id */ "reader");

                    this.html5Qrcode!.start(
                        this.cameraId, 
                        {
                            fps: 10,    // Optional, frame per seconds for qr code scanning
                            qrbox: { width: 250, height: 250 }  // Optional, if you want bounded box UI
                        },
                        (decodedText: string, decodedResult) => {
                            // do something when code is read
                            this.myScanSuccess(decodedResult);
                            this.scanEnd();
                        },
                        (errorMessage) => {
                            // камера долго грузится, поэтому чтобы не было пустового окна без камеры
                            // как только камера считывать первые данные убираю загрузку
                            if(this.loading) {
                                this.loading = false;
                                this.showCloseBtn = true;
                            }
                            // parse error, ignore it.
                        }
                    )
                    .catch((err) => {
                    // Start failed, handle it.
                    });
                }
            }).catch(err => {
                // handle err
                if(err && err.match && err.match('NotAllowedError') != null) {
                    //Если не предоставели права на камеру сообщаем об этом
                    this.scanEnd();
                    this.openAlertNoCameraPermission(); 
                }
            });
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
        width: 660px;
        height: 554px;
    }
    .reader {
        width: 600px;
    }
    .reader-block--padding {
        padding: 30px;
    }
</style>