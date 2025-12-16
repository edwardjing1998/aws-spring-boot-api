Type 'React.Dispatch<React.SetStateAction<import("c:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/src/modules/edit/client-information/utils/ClientInformationWindow").ClientGroupRow | null>>' is not assignable to type 'React.Dispatch<React.SetStateAction<import("c:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/src/modules/edit/client-information/EditClientInformation").ClientGroupRow | null>>'.
  Type 'React.SetStateAction<import("c:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/src/modules/edit/client-information/EditClientInformation").ClientGroupRow | null>' is not assignable to type 'React.SetStateAction<import("c:/Users/F2LIPBX/react/fiserv-github/react-rapid-admin/src/modules/edit/client-information/utils/ClientInformationWindow").ClientGroupRow | null>'.

  admin/src/modules/edit/client-information/utils/ClientInformationWindow").ClientGroupRow | null>'.
    Type 'ClientGroupRow' is not assignable to type 'SetStateAction<ClientGroupRow | null>'.
      Type 'ClientGroupRow' is missing the following properties from type 'ClientGroupRow': clientEmail, sysPrinsPrefixests(2322)
EditClientInformation.tsx(47, 3): The expected type comes from property 'setSelectedGroupRow' which is declared here on type 'IntrinsicAttributes & EditClientInformationProps'
