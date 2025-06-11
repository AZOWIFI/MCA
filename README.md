import { useState, useEffect } from "react";

export default function App() {
  const [macList, setMacList] = useState([]);
  const [mac, setMac] = useState("");
  const [device, setDevice] = useState("");
  const [entryTime, setEntryTime] = useState("");

  const addMacEntry = (e) => {
    e.preventDefault();
    if (mac && device && entryTime) {
      setMacList([...macList, { mac, device, entryTime: new Date(entryTime) }]);
      setMac("");
      setDevice("");
      setEntryTime("");
    }
  };

  const calculateStatus = (entry) => {
    const now = new Date();
    const expiryTime = new Date(entry.entryTime.getTime() + 24 * 60 * 60 * 1000);
    if (now >= expiryTime) {
      const diffHours = Math.floor((now - expiryTime) / (1000 * 60 * 60));
      return `Expired ${diffHours}h ago`;
    } else {
      const remainingHours = Math.ceil((expiryTime - now) / (1000 * 60 * 60));
      return `${remainingHours}h remaining`;
    }
  };

  useEffect(() => {
    const interval = setInterval(() => setMacList([...macList]), 60000);
    return () => clearInterval(interval);
  }, [macList]);

  return (
    <div className="p-4 max-w-3xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">MAC Expiry Tracker</h1>
      <form onSubmit={addMacEntry} className="grid grid-cols-1 gap-2 md:grid-cols-4 mb-4">
        <input
          type="text"
          placeholder="MAC Address"
          value={mac}
          onChange={(e) => setMac(e.target.value)}
          className="border p-2 rounded"
          required
        />
        <input
          type="text"
          placeholder="Device Name"
          value={device}
          onChange={(e) => setDevice(e.target.value)}
          className="border p-2 rounded"
          required
        />
        <input
          type="datetime-local"
          value={entryTime}
          onChange={(e) => setEntryTime(e.target.value)}
          className="border p-2 rounded"
          required
        />
        <button type="submit" className="bg-blue-500 text-white rounded p-2">
          Add
        </button>
      </form>

      <div className="overflow-x-auto">
        <table className="w-full border-collapse">
          <thead>
            <tr className="bg-gray-200">
              <th className="border p-2">MAC Address</th>
              <th className="border p-2">Device Name</th>
              <th className="border p-2">Entry Time</th>
              <th className="border p-2">Status</th>
            </tr>
          </thead>
          <tbody>
            {macList.map((entry, index) => (
              <tr key={index}>
                <td className="border p-2">{entry.mac}</td>
                <td className="border p-2">{entry.device}</td>
                <td className="border p-2">{entry.entryTime.toLocaleString()}</td>
                <td
                  className={`border p-2 font-semibold ${calculateStatus(entry).includes("Expired") ? "text-red-600" : "text-green-600"}`}
                >
                  {calculateStatus(entry)}
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  );
}
