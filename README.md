

import { Route, BrowserRouter as Router, Routes } from "react-router";

import AdminMain from "./RapidAdmin/AdminMain";

import AccountNumberComponent from "./Rapid/Onload/Components/AccountNumberComponent";
import ImbMainComponent from "./Rapid/RapidImb/Components/ImbMainComponent";


function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<AccountNumberComponent />} />
        <Route path="/admin/*" element={<AdminMain />} />
        <Route path="/Imb" element={<ImbMainComponent />} />
      </Routes>
    </Router>
  );
}

export default App;
