# Swaad Food Delivery documentation

This repository is the durable control center for the Swaad multi-repository project.

Historical design inputs remain at the repository root. Current project state, source inventory, traceability, backlog, blockers, and resume instructions live under [`project/`](project/).

## Current status

The customer, restaurant-owner, driver and operations demo journeys have local browser and CI evidence. The September 12 checkpoint adds actual OTP login through a private mock SMS transport and real MinIO document upload/rejection/replacement/resubmission/approval. Feature PRs are pushed but unmerged; do not equate implemented scope with a released application. Shared deployment, scheduler/quota telemetry and real providers remain unverified or deliberately out of demo scope. See [`project/RESUME.md`](project/RESUME.md), [`project/STATE.json`](project/STATE.json), [`project/RELEASE_MANIFEST.json`](project/RELEASE_MANIFEST.json), and [`project/CAPACITY_AND_COST.md`](project/CAPACITY_AND_COST.md).

The first actual order-service integration is now published: [order-service PR1](https://github.com/SwaadFoodDelivery/order-service/pull/1), [backend PR18](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/18), and [proto PR1](https://github.com/SwaadFoodDelivery/proto/pull/1). Owned reads cross real gRPC and PostgreSQL; checkout/write ownership remains with the backend during staged extraction. See the [backend walkthrough](walkthroughs/backend/order-service-grpc.md) and [auth/onboarding walkthrough](walkthroughs/frontend/auth-onboarding.md).

## Historical inputs

- [`FoodDelivery_LLD_Final _review.pdf`](FoodDelivery_LLD_Final%20_review.pdf)
- [`food_delivery_app_db_design_final_review .pdf`](food_delivery_app_db_design_final_review%20.pdf)
- [`flowcharts/`](flowcharts/)

## Project control documents

- [`MASTER_PLAN.md`](project/MASTER_PLAN.md)
- [`SOURCE_INVENTORY.md`](project/SOURCE_INVENTORY.md)
- [`REQUIREMENTS_TRACEABILITY.md`](project/REQUIREMENTS_TRACEABILITY.md)
- [`BACKLOG.md`](project/BACKLOG.md)
- [`BLOCKERS.md`](project/BLOCKERS.md)
- [`SESSION_LOG.md`](project/SESSION_LOG.md)
- [`RELEASE_MANIFEST.json`](project/RELEASE_MANIFEST.json)
- [`CAPACITY_AND_COST.md`](project/CAPACITY_AND_COST.md)
