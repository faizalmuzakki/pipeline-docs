# Project Work Order Endpoints

## Vendor & Tab Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get vendor list for tabs | /api/v3/project/work_order/get_vendor_list | GET |
| Get vendor data | /api/v3/project/work_order/vendor_list_data | GET |
| Rearrange vendor tabs | /api/v3/project/work_order/rearrange_vendor_tabs | POST |

## Production & Detail Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get production detail data | /api/v3/project/work_order/production_detail_data/{id} | GET |

## Lot Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create/Update lot | /api/v3/project/lot_work_order | POST |
| Delete lot | /api/v3/project/lot_work_order/{id} | DELETE |

## PQT (Process Qualification Test) Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get PQT item options | /api/v3/project/work_order/pqt_item_options | GET |
| Get PQT spec options | /api/v3/project/work_order/pqt_spec_options | GET |
| Submit PQT form | /api/v3/project/work_order/pqt_form | POST |
| Get PQT detail | /api/v3/project/work_order/pqt_detail/{id} | GET |
| Delete PQT | /api/v3/project/work_order/delete_pqt/{id} | DELETE |

## Length History & Reports

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get length history data | /api/v3/project/work_order/length_history_list_data | GET |
| Get history filename | /api/v3/project/work_order/get_history_filename | GET |
| Resolve length history | /api/v3/project/work_order/resolve_length_history | POST |
| Submit length report | /api/v3/project/work_order/submit_length_report | POST |
| Get existing report file | /api/v3/project/work_order/get_existing_report_file | GET |
| Get item spec options | /api/v3/project/work_order/length_history_item_spec_options/{id} | POST |
| Delete report | /api/v3/project/work_order/delete_report/ | POST |
| Delete item from report | /api/v3/project/work_order/delete_item_from_report/ | POST |
| Download history photos | /api/v3/project/work_order_history/download_photos/{id_history}/{index} | GET |

## Inspection Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get inspection datatables | /api/v3/project/inspection/datatables | GET |
| Get work order inspection data | /api/v3/project/work_order/inspection/{id} | GET |
| Submit inspection | /api/v3/project/inspection | POST |
| Upload report attachment | /api/v3/project/inspection/upload_report_attachment | POST |
| Store inspection mapping | /api/v3/project/inspection/mapping | POST |
| Edit inspection | /api/v3/project/inspection/{id}/edit | GET |
| Delete inspection | /api/v3/project/inspection/{id} | DELETE |
| Delete inspection queue | /api/v3/project/inspection/delete-queue/{id} | DELETE |
| Get inspection queue | /api/v3/project/inspection/getQueue/{id}/{test_type}/{id_spec?} | GET |
| Get inspection detail | /api/v3/project/inspection/{id}/{test_type?} | GET |
| Get inspection detail SOW | /api/v3/project/inspection/detail_sow/{id}/{test}/{stage} | GET |
| Get inspection detail WO | /api/v3/project/inspection/detail_wo/{wo_id}/{test}/{stage} | GET |
| Add inspection note | /api/v3/project/inspection/note/{id} | POST |
| Get inspection status | /api/v3/project/inspection/queue-status | GET |
| Download inspection template | /api/v3/project/inspection/download-template | GET |
| Download queue file | /api/v3/project/inspection/download_queue_file/{id} | GET |

## Inspection - Table & Graph Views

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get table testing data | /api/v3/project/inspection/table/getTable | GET |
| Get graph testing data | /api/v3/project/inspection/graph/getGraph | GET |
| Get distinct options | /api/v3/project/inspection/table/getDistinctOption | GET |
| Export inspection data | /api/v3/project/inspection/table/export | POST |

## Inspection - Report & Mapping

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get report datatables | /api/v3/project/inspection/datatables_report | GET |
| Get report mapping datatables | /api/v3/project/inspection/datatables_report_mapping | GET |
| Get report options | /api/v3/project/inspection/report_options | GET |
| Get PDF report mapping | /api/v3/project/inspection/report_pdf_mapping | GET |
| Save mapping report | /api/v3/project/inspection/save_mapping_report | POST |
| Delete report | /api/v3/project/inspection/delete_report | POST |
| Get inspection mapping form | /api/v3/project/inspection/mapping/{id} | GET |
| Get mapping attributes | /api/v3/project/inspection/mapping/attributes | GET |
| Get mapping units | /api/v3/project/inspection/mapping-units | GET |
| Get title mapping | /api/v3/project/inspection/title-mapping | GET |

## Pipe Receiving Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get pipe receiving datatables | /api/v3/project/pipe_receiving/datatables | GET |
| Submit pipe receiving | /api/v3/project/pipe_receiving | POST |
| Get pipe receiving detail | /api/v3/project/pipe_receiving/{id} | GET |
| Get pipe receiving form for edit | /api/v3/project/pipe_receiving/{id}/edit | GET |
| Add pipe receiving note | /api/v3/project/pipe_receiving/note/{id} | POST |
| Delete pipe receiving | /api/v3/project/pipe_receiving/{id} | DELETE |
| Delete pipe receiving queue | /api/v3/project/pipe_receiving/delete-queue/{id} | DELETE |
| Get pipe receiving queue | /api/v3/project/pipe_receiving/getQueue/{id} | GET |
| Get pipe receiving table data | /api/v3/project/pipe_receiving/table/getTable | GET |
| Get pipe receiving graph data | /api/v3/project/pipe_receiving/graph/getGraph | GET |
| Get pipe receiving distinct options | /api/v3/project/pipe_receiving/table/getDistinctOption | GET |
| Export pipe receiving data | /api/v3/project/pipe_receiving/table/export | POST |

## Material Test Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create material test | /api/v3/project/material_test | POST |
| Check duplicate batch number | /api/v3/project/material_test/check_duplicate | GET |
| Delete material test | /api/v3/project/material_test/{id} | DELETE |
| Download material test attachment | /api/v3/project/material_test/download/{id} | GET |
| Get material test form | /api/v3/project/material_test/{id} | GET |
| Get material test inspection form | /api/v3/project/inspection/material_test/{id} | GET |

## Weld Repair Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get weld repair datatables | /api/v3/project/weld_repair/datatables | GET |
| Get weld repair result datatables | /api/v3/project/weld_repair_result/datatables | GET |
| Submit weld repair | /api/v3/project/weld_repair | POST |
| Update weld repair | /api/v3/project/weld_repair/{id} | POST |
| Get weld repair detail | /api/v3/project/weld_repair/{id} | GET |
| Delete weld repair | /api/v3/project/weld_repair/{id} | DELETE |
| Export weld repair | /api/v3/project/weld_repair/export/{id?} | GET |

## Tally List Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get tally list amount of data | /api/v3/project/get-tally-list-amount-of-data | GET |
| Get tally list datatables | /api/v3/project/tally_list/datatables | GET |
| Get tally files | /api/v3/project/tally_file | GET |
| Get existing tally file detail | /api/v3/project/tally_file/existing | GET |
| Get tally data | /api/v3/project/tally_file/data | GET |
| Get tally file datatables | /api/v3/project/tally_file/datatables | GET |
| Get transfer tally file datatables | /api/v3/project/transfer_tally_file/datatables | GET |
| Get pipe details datatables | /api/v3/project/pipe_details/datatables | GET |
| Get transfer pipe details datatables | /api/v3/project/transfer_pipe_details/datatables | GET |
| Check can upload tally | /api/v3/project/tally_list/can_upload_tally/{id_cargo} | GET |
| Upload tally list | /api/v3/project/tally_list/upload | POST |
| Import tally list | /api/v3/project/tally_list/import/{id} | POST |
| Transfer import tally | /api/v3/project/tally_list/transfer_import/{id} | POST |
| Edit tally remark | /api/v3/project/tally_list/edit_remark | POST |
| Edit transfer tally data | /api/v3/project/transfer_tally_file/edit_remark | POST |
| Download tally | /api/v3/project/tally_list/download/{id} | GET |
| Download tally queue file | /api/v3/project/tally_list/download-queue-file/{id} | GET |
| Download cutting document | /api/v3/project/tally_list/download_cutting_document | POST |
| Delete tally list | /api/v3/project/delete_tallylist | POST |
| Delete tally queue file | /api/v3/project/tally_list/delete-queue-file/{id} | DELETE |

## Stress Strain Curve Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get stress strain curve datatables | /api/v3/project/stress_strain_curve/datatables | GET |
| Get stress strain curve detail | /api/v3/project/stress_strain_curve/{id} | GET |
| Get stress strain curve graph | /api/v3/project/stress_strain_curve/get_graph/{id} | GET |
| Create stress strain curve | /api/v3/project/stress_strain_curve | POST |
| Create stress strain curve mapping | /api/v3/project/stress_strain_curve-mapping | POST |
| Delete stress strain curve | /api/v3/project/stress_strain_curve/{id} | DELETE |
| Export stress strain curve | /api/v3/project/stress_strain_curve/export | POST |

## Overall Work Order Progress

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get overall work orders | /api/v3/project/overall_work_order | GET |
| Submit overall work orders | /api/v3/project/overall_work_order | POST |
| Get reverse overalls | /api/v3/project/reverse/progress/{id}/overall_work_order | GET |
| Submit reverse overalls | /api/v3/project/reverse/overall_work_order | POST |
| Get progress statistics | /api/v3/project/overall_work_order/progress/stats | GET |
| Get chart statistics | /api/v3/project/overall_work_order/chart/stats/{key} | GET |
| Check receiving progress | /api/v3/project/overall_work_order/check_receiving/{id} | GET |
| Update forecast date | /api/v3/project/overall_work_order/update_forecast_date | POST |
| Check curve status | /api/v3/project/overall_work_order/check_curve_status | GET |
| Update document submission | /api/v3/project/overall_work_order/update_doc_sub | POST |
| Download report file | /api/v3/project/overall_work_order/download_report_file | POST |
