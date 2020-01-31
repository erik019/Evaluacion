<template>
    <div>
        <h2 class="content-heading">Galeria
            <button type="button" class="btn btn-primary" @click="CargarImagen()">
                Cargar Imagen
            </button>
        </h2>
        <div class="row">
            <div class="col-md-12" v-for="imagen in imagenes" :key="imagen.index">

                <div id="mdb-lightbox-ui"></div>

                <div class="mdb-lightbox">

                <figure class="col-md-4">
                    <a :href="imagen.image" data-size="1600x1067">
                    <img alt="picture" :src="imagen.image" class="img-fluid">
                    </a>
                    <a class="btn btn-danger" @click="eliminarImagen(imagen)">Eliminar</a>
                </figure>

                </div>

            </div>
        </div>
        <!-- modal banner  -->
        <div class="modal fade" id="cargarImagen" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
            Change class .modal-sm to change the size of the modal 
            <div class="modal-dialog modal-lg" role="document">


                <div class="modal-content">
                    <div class="modal-header">
                        <h4 class="modal-title w-100" id="myModalLabel">Cargar imagen</h4>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <form enctype="multipart/form-data">
                            <label for="photo">Seleccionar imagen</label>
                            <input type="file" @change="obtenerImagen" class="form-control-file">
                            <figure>
                                <img with="200" height="200" :src="imagenPreview" alt="image">
                            </figure>
                        </form>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary btn-sm" data-dismiss="modal">Close</button>
                        <button type="button" class="btn btn-primary btn-sm" @click="enviarImagen">Save changes</button>
                    </div>
                </div>
            </div>
        </div>
         <!--termina modal banner -->
    </div>
</template>

<script>
export default {
    data(){
            return{
            /* imagenes preview */ 
             imagenMiniatura:'',

            /* Objetos para obtener los datos que se van a cargar*/
                gallery:{
                    'user_id': '',
                    'image': ''
                },

            /* Arreglo vacio para almacenar los datos a mostrar */
                imagenes:[]
            }
        },
    created(){
            this.obtenerDatos();
        },
    methods: {

        obtenerDatos: function(){
                let URL = '/Gallery';
                axios.get(URL).then((response)=>{
                    this.imagenes = response.data;
                });     
        },
        CargarImagen: function(){
                    $('#cargarImagen').modal('show');
        },
        obtenerImagen(a){
            let fileImagen = a.target.files[0];
            this.gallery.image = fileImagen;
            this.cargarImagen(fileImagen);
        },
        cargarImagen(fileImagen){
            let reader = new FileReader();
            reader.onload = (a) => {
                this.imagenMiniatura = a.target.result;
            }
            reader.readAsDataURL(fileImagen);
        },
        enviarImagen(){
  
                let formData = new FormData();
                formData.append('image', this.gallery.image);

               let URL = '/Gallery/store';

                axios.post( URL,
                    formData,
                    {
                        headers: {
                            'Content-Type': 'multipart/form-data'
                        }
                    }
                ).then(() => {
                    $('#cargarImagen').modal('hide');
                    this.obtenerDatos();
                });
        },

         eliminarImagen: function(imagen){
                let URL = '/Gallery/delete/' + imagen.id;
                axios.delete(URL).then((response) => {
                    this.obtenerDatos();
                    });      
            },
    },
    computed: {
        imagenPreview(){
            return this.imagenMiniatura
        }
    }
}
</script>
