<script setup>
import { vaah } from '../../../vaahvue/pinia/vaah'
import { useContentStore } from '../../../stores/store-contents'

const store = useContentStore();
const useVaah = vaah();

</script>

<template>

    <div v-if="store.list">
        <!--table-->
         <DataTable :value="store.list.data"
                       dataKey="id"
                   class="p-datatable-sm"
                   v-model:selection="store.action.items"
                   stripedRows
                   responsiveLayout="scroll">

             <Column selectionMode="multiple"
                    v-if="store.isViewLarge()"
                    headerStyle="width: 3em">
            </Column>

             <Column field="id" header="ID" v-if="store.isViewLarge()"
                    :style="{width: store.getIdWidth()}" :sortable="true">
            </Column>

             <Column field="name" header="Name"
                    :sortable="true">

                <template #body="prop">
                    <Badge v-if="prop.data.deleted_at"
                           value="Trashed"
                           severity="danger"></Badge>
                    <a v-if="prop.data.id != store.route.params.id"
                       href="javascript:void(0)"   @click="store.toEdit(prop.data)">
                    {{prop.data.name}}
                    </a>
                    <span v-else>
                        {{prop.data.name}}
                    </span>

                </template>

            </Column>

             <Column field="permalink" header="Permalink"
                     v-if="store.isViewLarge()"
                     :sortable="true">

                 <template #body="prop">
                     <Badge v-if="prop.data.deleted_at"
                            value="Trashed"
                            severity="danger"></Badge>
                     {{prop.data.permalink}}
                 </template>

             </Column>


             <Column field="updated_at" header="Updated"
                        v-if="store.isViewLarge()"
                        style="width:150px;"
                        :sortable="true">

                    <template #body="prop">
                        {{useVaah.ago(prop.data.updated_at)}}
                    </template>

                </Column>

             <Column field="status" v-if="store.isViewLarge()"
                    :sortable="true"
                    style="width:100px;"
                    header="Status">

                <template #body="prop">
                    {{useVaah.toLabel(prop.data.status)}}
                </template>

            </Column>

             <Column field="actions" style="width:150px;"
                    v-if="store.isViewLarge()"
                    :style="{width: store.getActionWidth() }"
                    :header="store.getActionLabel()">

                <template #body="prop">
                    <div class="p-inputgroup ">

                        <Button class="p-button-tiny p-button-text"
                                data-testid="contents-table-to-external-ink"
                                v-tooltip.top="'View Content'"
                                @click="store.toExternalLink(prop.data)"
                                icon="pi pi-external-link" />

                        <Button class="p-button-tiny p-button-text"
                                data-testid="contents-table-to-view"
                                v-tooltip.top="'View'"
                                @click="store.toView(prop.data)"
                                icon="pi pi-eye" />

                        <Button class="p-button-tiny p-button-text"
                                data-testid="contents-table-to-edit"
                                v-tooltip.top="'Update'"
                                @click="store.toEdit(prop.data)"
                                icon="pi pi-pencil" />

                        <Button class="p-button-tiny p-button-danger p-button-text"
                                data-testid="contents-table-action-trash"
                                v-if="store.isViewLarge() && !prop.data.deleted_at"
                                @click="store.itemAction('trash', prop.data)"
                                v-tooltip.top="'Trash'"
                                icon="pi pi-trash" />


                        <Button class="p-button-tiny p-button-success p-button-text"
                                data-testid="contents-table-action-restore"
                                v-if="store.isViewLarge() && prop.data.deleted_at"
                                @click="store.itemAction('restore', prop.data)"
                                v-tooltip.top="'Restore'"
                                icon="pi pi-replay" />


                    </div>

                </template>


            </Column>


        </DataTable>
        <!--/table-->

        <!--paginator-->
        <Paginator v-model:rows="store.query.rows"
                   :totalRecords="store.list.total"
                   @page="store.paginate($event)"
                   :rowsPerPageOptions="store.rows_per_page"
                   class="bg-white-alpha-0 py-2"
                   template="PrevPageLink PageLinks NextPageLink RowsPerPageDropdown"
                   :pt="{ root: 'p-0 pt-1'}"
        >
        </Paginator>
        <!--/paginator-->

    </div>

</template>
