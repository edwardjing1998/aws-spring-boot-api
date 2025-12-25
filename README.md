// EditInvalidedArea.types.ts
export type InvalidAreaId = { sysPrin: string; area: string };

export interface InvalidAreaResponse {
  id?: InvalidAreaId;
  ok?: boolean;
  [key: string]: any;
}
