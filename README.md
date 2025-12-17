ype '{ height: string; fontSize: string; width: string; maxWidth: string; paddingLeft: string; scrollbarWidth: "none"; msOverflowStyle: string; }' is not assignable to type 'Properties<string | number, string & {}>'.
  Types of property 'msOverflowStyle' are incompatible.
    Type 'string' is not assignable to type 'MsOverflowStyle | undefined'.ts(2322)
index.d.ts(2747, 9): The expected type comes from property 'style' which is declared here on type 'IntrinsicAttributes & CFormSelectProps & RefAttributes<HTMLSelectElement>'
