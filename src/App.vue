<template>
  <div id="app">
    <homeIndex />
    
    <!-- loading -->
    <div class="modal" id="Loading_dialog" tabindex="-1" role="dialog" data-backdrop="static" data-keyboard="false">
        <div class="modal-dialog modal-dialog-centered modal-sm" role="document">
            <div class="modal-content">
                <div class="modal-body">
                    <div class="d-flex justify-content-center">
                        <div class="spinner-grow" role="status">
                            <span class="sr-only">Loading...</span>
                        </div>
                    </div>
                    <div style="margin: 10px">
                        <h5 class="text-center">
                            <span>Loading(⁎⁍̴̛ᴗ⁍̴̛⁎)</span>
                        </h5>
                    </div>
                </div>
            </div>
        </div>
    </div>
  </div>
  
</template>

<script>
import homeIndex from './views/Home/index.vue'

export default {
  name: 'App',
  components: {
    homeIndex
  }
}
</script>
