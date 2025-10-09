# Project Transfer Endpoints

## Transfer Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create transfer | /api/v3/project/transfer | POST |
| Get transfer for edit | /api/v3/project/transfer/{id}/edit | GET |
| Delete transfer | /api/v3/project/transfer/{id} | DELETE |
| Get transfer route details | /api/v3/project/transfer_route_details/{id} | GET |
| Get transfer cargo details | /api/v3/project/transfer_cargo/{id} | GET |
| Get transfer route data | /api/v3/project/transfer_route | GET |

## Transfer Options & Selectors

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get from options | /api/v3/project/transfer_from_options/{id?} | GET |
| Get consignee options | /api/v3/project/transfer_consignee_options/{id_vendor?} | GET |
| Get cargo options | /api/v3/project/transfer_cargo_options | GET |
| Get delivery options | /api/v3/project/transfer_delivery_options | GET |
| Get work order options | /api/v3/project/wo_options | GET |
| Get lot options | /api/v3/project/wo/lots/filter/{id} | GET |

## Cargo Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create/Update cargo | /api/v3/project/cargo | POST |
| Get cargo for edit | /api/v3/project/cargo/{id}/edit | GET |
| Delete cargo | /api/v3/project/cargo/{id} | DELETE |
| Get cargo progress | /api/v3/project/cargo_progress/{id_cargo}/{id_shipment} | GET |
| Submit cargo progress | /api/v3/project/cargo_progress | POST |

## Shipment Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Create/Update shipment | /api/v3/project/shipment | POST |
| Get shipment details | /api/v3/project/shipment/{id} | GET |
| Get shipment for edit | /api/v3/project/shipment/{id}/edit | GET |
| Update shipment | /api/v3/project/shipment/{id} | POST |
| Delete shipment | /api/v3/project/shipment/{id} | DELETE |
| Get shipment progress | /api/v3/project/shipment_progress/{id} | GET |
| Submit shipment progress | /api/v3/project/shipment_progress | POST |
| Update shipment date | /api/v3/project/shipment_date | POST |

## Tally Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get tally options | /api/v3/project/transfer_tally_options | GET |
| Check complete tally quantity | /api/v3/project/check_complete_tally_qty | POST |
| Complete tally | /api/v3/project/complete_tally | POST |
| Confirm date | /api/v3/project/confirm_date | POST |
| Edit tally remark | /api/v3/project/transfer_tally_file/edit_remark | POST |

## Tracking & Vessel Management

| API Name | API Route | HTTP Method |
|----------|-----------|-------------|
| Get tracking details | /api/v3/project/transfer_tracking | POST |
| Check duplicate vessel | /api/v3/project/duplicate_vessel | POST |
