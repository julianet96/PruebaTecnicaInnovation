<template>
    <transition name="modal">
    <div class="modalMask">
      <div class="modalWrapper">
        <div class="modalContainer">
          <!-- Apartado del texto del modal -->
          <div class="modalHeader">
            <slot name="header">
              default header
            </slot>
          </div>

          
          <!-- Apartado de botones del modal -->
          <div class="modalFooter">
            <slot name="footer">
              <button class="modalButton" @click="$emit('close')">
                OK
              </button>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'modal'
}

</script>
<style scoped>
.modalMask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modalWrapper {
  display: table-cell;
  vertical-align: middle;
}

.modalContainer {
  width: 400px;
  margin: 0px auto;
  padding: 50px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
  transition: all .3s ease;
}


.modalButton {
  float: right;
  padding: 10px;
}


</style>