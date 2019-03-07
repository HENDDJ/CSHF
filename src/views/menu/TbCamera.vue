<template>
    <section>
        <CommonCRUD :columns="formColumns" api-root="identity/tbCamera" :formColumns="formColumns"></CommonCRUD>
    </section>
</template>

<script>
    import CommonCRUD from '@/components/CommonCRUD';
    export default {
        name: 'TbCamera',
        data() {
            return {
            }
        },
        created(){
            this.formColumns = this.$store.state.classInfo.properties

        },
        components: {
            CommonCRUD
        }
    };
</script>

<style scoped>

</style>
