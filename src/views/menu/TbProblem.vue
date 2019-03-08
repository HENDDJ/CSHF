<template>
    <section>
        <CommonCRUD :columns="Columns" api-root="identity/tbProblem" :formColumns="formColumns"></CommonCRUD>
    </section>
</template>

<script>
    import CommonCRUD from '@/components/CommonCRUD';
    export default {
        name: 'TbProblem',
        data() {
            return {
            }
        },
        created(){
            this.formColumns = this.$store.state.classInfo.properties
            this.Columns = this.$store.state.classInfo.properties
            let org = JSON.parse(sessionStorage.getItem('userInfo')).id;
            this.$http('GET', `identity/principal/${org}id`, false).then(
                data => {
                    console.log(data)
                    this.formColumns.filter(item => item.name === 'submitter')[0].value = data.name;
                    let myDate = new Date();
                    this.formColumns.filter(item => item.name === 'submissionTime')[0].value = Date(myDate.toLocaleDateString());
                }
            )

        },
        medthods :{
        },
        components: {
            CommonCRUD
        }
    };
</script>

<style scoped>

</style>
