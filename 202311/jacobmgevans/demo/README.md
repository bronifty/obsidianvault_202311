- Database
```ts
export class Database implements IDatabase {

data: DataRecordType[];

constructor(initialData: DataRecordType[]) {

this.data = initialData;

}

select(): DatabaseDataType {

return this.data;

}

insert(dataRecord: DataRecordType): ResultMessage {

this.data.push(dataRecord);

return { result: "success" };

}

clearData(): void {

this.data.length = 0;

}

}
```