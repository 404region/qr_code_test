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
<script lang="ts">
    import { defineComponent } from "vue";
    import {Html5Qrcode} from "html5-qrcode";
import { Html5QrcodeResult, QrcodeResult } from "html5-qrcode/esm/core";

    export default defineComponent({
        name: 'MyQrCode',
        data() {
            return {
                qrCodeText: '' as string,
                html5Qrcode: {} as Html5Qrcode,
                showModal: false as boolean,
                cameraId: '' as string,
            }
        },
        methods: {
            myScanSuccess(decodedResult: Html5QrcodeResult) {
                // Скрываем\показываем элементы, отображаем результат
                this.qrCodeText = decodedResult.decodedText;
            },
            scanEnd() {
                this.showModal = false;
                (this.html5QrCode as Html5Qrcode).stop().then((ignore) => {
                // QR Code scanning is stopped.
                }).catch((err) => {
                // Stop failed, handle it.
                });

            },
            scanIt() {
                const vueObj = this;
                vueObj.showModal = true;
                Html5Qrcode.getCameras().then(devices => {
                    /** 
                     * devices would be an array of objects of type: 
                     * { id: "id", label: "label" }
                     */
                    if (devices && devices.length) {
                        vueObj.cameraId = devices[0].id; 
                        // .. use this to start scanning.
                        vueObj.html5QrCode = new Html5Qrcode(/* element id */ "reader");
                        (vueObj.html5QrCode as Html5Qrcode).start(
                            vueObj.cameraId, 
                            {
                                fps: 10,    // Optional, frame per seconds for qr code scanning
                                qrbox: { width: 250, height: 250 }  // Optional, if you want bounded box UI
                            },
                            (decodedText: string, decodedResult) => {
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
            }
        }
    })
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