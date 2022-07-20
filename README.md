## PostalOversize



##Процедура - создать посылку



```sql

exec [PS].[PostalItemInsertNew_01_tran_Oversize]
@isJuristic =0,					--тип отправителя (0 - физическое лицо, 1 - юридическое лицо)
@GoodsId =836884,							--Id услуги (836884-посылка)
@PostDeliveryTypeId =1,					--Id типа доставки (1-отделение-отделение, 2-отделение-дверь)
@PostalWeightId =14,						--Id диапазона веса (1-0-3кг, 2-3-10кг, 3-10-20кг)
@ContractorId =0,						--Id контрагента (если отправитель физическое лицо – 0)
@isFragile =1,						--хрупкость (1 – есть, 0 – нет)
@isInventory =1,						--опись (1 – есть, 0 – нет)
--@ContractorManId bigint=0,					--Id пользователя привязанного к контрагенту (отправитель - юридическое лицо)
@ManDiscountId =3922635,					--Id пользователя (отправитель - физическое лицо)
@WarehouseidStart =70130010,					--ОПС поступления почтовой услуги
@WarehouseidFinish =70130010,				--ОПС выдачи почтовой услуги
--@Adress1IdReciever bigint=null,				--Id адреса получателя
@PhoneNumberReciever =375257405752,			--контактный телефон получателя
@IsJuristicReciever =0,				--тип получателя (0 - физическое лицо, 1 - юридическое лицо)
@Name1Reciever =TAX,				--фамилия получателя
@Name2Reciever =TAX,				--имя получателя
--@Name3Reciever ='',			--отчество получателя
--@CashOnDeliveryMoneyBackId bigint=0,		--за чей счет пересылка наложенного платежа (0 - за счет получателя, 1 - за счет отправителя).
--@InfoSender nvarchar(1000)=null,			--комментарий отправителя
@isCompletenessCheck =1				--проверка комплектации (1 – есть, 0 – нет)
--,@PostalItemExternalId nvarchar(200)=null	--внешний Id посылки 
--,@SourceId =1							--откруда обращение к процедуре (1-из API Postal.PutOrder (Jwt), 2-из PS.p_Postal_ChangeOrder) 
--,@PostalItemIdAlt bigint=0					--при обращении из PS.p_Postal_ChangeOrder (PostalItemId, в котором надо внести изменения)
--,@LoginId bigint=0							--из Jwt если юрик-ManId, если физик-ManDiscountId
--,@LoginTypeId bigint=0						--из Jwt тип пользователя - 1-юрик, 2-физик
--,@IsExternal bigint=-1						--из Jwt - обращение из внешнего API (от контрагентов)
--,@LoginIdFrom bigint=0						--если юрик-ManId, если физик-ManDiscountId (для ContractorIdFrom)
--,@LoginTypeIdFrom bigint=0					--тип пользователя - 1-юрик, 2-физик (для ContractorIdFrom)
,@isOversize =1  --

```

##Процедура приёмки ПчО
```sql
exec ps.CheckXMoveTo_0_10_Oversize
 @PostalItemId =7016650,        --=35,    --запись журнала почтовых отправлений относительно которой осуществляется приемка
@GoodsType =8,         --=9,    --Тип почтового отправления (денежный перевод или почтовоей отпроавление (письмо посылка и проечее))
@UpdateOrInsert =1,        --=1,    --Параметр обнолять или вставлять запись в журнале ПчО (0-update, 1 - insert)
@PostalTariffId =979,        --=10,    --Идентификатор почтового тарифа
@PostalWeightIdNew =14,       --=1,    --Диапазон веса
@PostalHeightNew =0,        --=115,    --Высота почтового отправления
@PostalWidthNew =0,        --=75,    --Ширина почтового отправления  
@PostalLentghNew =0,        --=86,    --Длина почтового отпарвления
@GoodsIdNew =836884,         --=836884,   --Тип почтового отправления
@isCashOnDeliveryNew =0,      --=1,    --Есть ли наложенный платеж
@CashOnDeliverySumNew =0,      --26,    --Величина наложенного платежа 
@CashOnDeliveryMoneyBackSumNew =0,    --=35.2,   --Величина за пересылку наложенного платежа
@isCashOnDeliveryDeclaredValueNew =0,   --=1,    --Есть ли объявленная ценность
@CashOnDeliveryDeclaredValueSumNew =0,   --=101,    --Величиана объявленной ценности
@CashOnDeliveryDeclaredValueTariffSumNew =0,  --=101,    --Составляющая тарифа за объявленную ценность 
@CashOnDeliveryImproveDocIdNew =0,    --=1,    --Нужен ли подтверждающий документ
@CashOnDeliveryImproveDocNameNew =0,  --''    --Наименование подтверждающего документа
@IsInventoryNew =0,        --=1,    --Имеется ли опись
@InventoryTariffSumNew =0,      --=35.01,   --Тариф за опись
@InventoryStuffSumNew =0,      --=35.89,   --Введенное самма описи оператором
@iFragileNew =0,        --=1,    --Наличие параметра хрупкость
@FragileTariffSumNew =0,       --=2.89,   --Тарфи за зрупкость
@ShipmentPriceWithTaxNew =4.99,      --56.3     --Итоговая стоимость пересылки
@PostOrderSumNew =0,        --величина денежного перевода  
@PostOrderTarifSumNew =0,       --тарифи за пересылку денежного перевода
@PostalWeightNew =1,        --вес почтового отправления
@UserId =6399,         --для хистори
@isPackagingNew =0,
@isGroupRec =0,        --Групповая приемка 1, обычная приемка 0
@CompletenessCheckPriceNew =0,
@isCompletenessCheckNew =0
,@isOversizeNEW =1 -- добавлено Громозкое
,@OversizeTariffSumNEW =2

```

##Процедура начисленяи платежа
```sql
EXEC	[PS].[PostalPaymentInsert_01]
		@PostalItemId = 7016650,
		@PaymentTypeId = 1,
		@PaymentDate = '20220101',
		@ForReports = 0
```
