<template>
    <Head title="Todas las dependencias"/>

    <BreezeAuthenticatedLayout>
        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
                    <div class="p-6 bg-white border-b border-gray-200">
                        <div class="flex justify-between mb-5">
                            <h1 class="text-2xl font-bold">Gestionar dependencias</h1>
                            <button
                                @click="openCreateUserModal"
                                class="p-2 text-center bg-principal text-white mx-1 rounded">Crear nueva
                            </button>
                        </div>

                        <table class="items-center bg-transparent border-collapse mx-auto ">
                            <thead>
                            <tr>
                                <th class="px-6 align-middle border border-solid py-3 uppercase border-l-0 border-r-0 whitespace-nowrap font-semibold text-left">
                                    Nombre dependencia
                                </th>

                                <th class="px-6 align-middle border border-solid py-3 uppercase border-l-0 border-r-0 whitespace-nowrap font-semibold text-left">
                                    Número de usuarios
                                </th>
                                <th class="px-6 align-middle border border-solid py-3 uppercase border-l-0 border-r-0 whitespace-nowrap font-semibold text-center">
                                    Acciones
                                </th>

                            </tr>
                            </thead>

                            <tbody>
                            <tr v-for="dependency in dependencies">
                                <td class="px-6 align-middle whitespace-nowrap p-4 text-left ">
                                    {{ dependency.name }}
                                </td>

                                <td class="px-6 align-middle whitespace-nowrap p-4 text-left ">
                                    {{ dependency.users.length }}
                                </td>
                                <td class="px-6 align-middle whitespace-nowrap p-4 text-center ">
                                    <Link :href="route('dependencies.users.index', {dependency:dependency.id})"
                                          class="p-2 text-center bg-green-600 text-white mx-1 rounded">Gestionar
                                        usuarios
                                    </Link>


                                    <button
                                        @click="deleteDependency(dependency.id)"
                                        class="p-2 text-center bg-red-600 text-white mx-1 rounded">Borrar
                                        dependencia
                                    </button>

                                </td>

                            </tr>

                            </tbody>

                        </table>
                    </div>
                </div>
            </div>
        </div>
    </BreezeAuthenticatedLayout>
</template>

<script>
import BreezeAuthenticatedLayout from '@/Layouts/Authenticated.vue'
import {Head} from '@inertiajs/inertia-vue3';
import {useForm} from '@inertiajs/inertia-vue3'
import {Link} from '@inertiajs/inertia-vue3'
import Swal from 'sweetalert2';

export default {
    setup() {
        const form = useForm({});
        return {form};
    },
    components: {
        BreezeAuthenticatedLayout,
        Head,
        Link
    },
    props: {
        dependencies: Array
    },

    methods: {

        openCreateUserModal: async function () {
            const {value} = await Swal.fire({
                showCancelButton: true,
                cancelButtonText: 'Cancelar',
                confirmButtonText: 'Crear dependencia',
                title: 'Crear una nueva dependencia',
                input: 'text',
                inputLabel: 'Nombre de la dependencia',
                inputPlaceholder: 'Ayudas educativas'
            })


            if (value === undefined) {
                return;
            }

            //Conseguir URL
            let url = route('dependencies.store');
            //Información a enviar
            let data = {
                name: value
            }

            try {
                let response = await axios.post(url, {
                    name: value
                });

                let modal = await Swal.fire('Creación de dependencia', ' Dependencia creada exitosamente', 'success')
                location.reload();

            } catch (e) {
                //Disparar ventana con información del error
                Swal.fire('Ha ocurrido un error', e.response.data, 'error')
            }


        },

        deleteDependency: async function (dependencyId) {
            let url = route('dependencies.destroy', {
                'dependency': dependencyId
            });
            try {
                let request = await axios.delete(url);
                await Swal.fire('Proceso exitoso', request.data, "success");
                location.reload();

            } catch (e) {
                //Disparar ventana con información del error
                Swal.fire('Ha ocurrido un error', e.response.data, 'error')
            }
        }

    }
}
</script>
