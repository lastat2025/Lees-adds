import React, { useState, useEffect } from 'react';
import { User, DollarSign, Shield, Star, Clock, Users, TrendingUp, Settings, Eye, EyeOff, CreditCard, FileText, Bell, LogOut, Lock, Activity, ArrowDownToLine } from 'lucide-react';

const LeesMoneyAdds = () => {
  const [userType, setUserType] = useState(null);
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [currentView, setCurrentView] = useState('home');
  const [contracts, setContracts] = useState([]);
  const [developers, setDevelopers] = useState([
    { 
      id: 1, 
      name: 'Premium Web Developer', 
      rating: 4.9, 
      price: '$50-100/hr', 
      specialties: ['React', 'Node.js', 'E-commerce'], 
      anonymous: true,
      contactHidden: true 
    },
    // ... other developers
  ]);
  // ... other state variables

  // Enhanced contract creation with security measures
  const createContract = (devId, advId, amount) => {
    const developer = developers.find(d => d.id === devId);
    const advertiser = advertisers.find(a => a.id === advId);
    
    if (developer.contactHidden || advertiser.contactHidden) {
      alert("Contact details will be revealed only after payment is secured");
    }
    
    const platformFee = amount * (adminStats.platformFee / 100);
    const developerPayout = amount - platformFee;
    
    const newContract = {
      id: contracts.length + 1,
      developerId: devId,
      advertiserId: advId,
      amount: amount,
      platformFee: platformFee,
      developerPayout: developerPayout,
      status: 'pending',
      created: new Date().toLocaleDateString(),
      watermark: `LMA-${Date.now()}-CONFIDENTIAL`
    };
    
    setContracts([...contracts, newContract]);
    setAdminStats(prev => ({
      ...prev,
      totalRevenue: prev.totalRevenue + platformFee,
      withdrawableBalance: prev.withdrawableBalance + platformFee,
      activeContracts: prev.activeContracts + 1
    }));
    
    // Add anti-circumvention monitoring
    monitorForCircumvention(devId, advId);
  };

  // Security measure to prevent cutting out the platform
  const monitorForCircumvention = (devId, advId) => {
    console.log(`Monitoring contract between dev ${devId} and adv ${advId} for circumvention attempts`);
    // In a real app, this would integrate with backend monitoring
  };

  // Withdraw funds to admin account
  const withdrawFunds = (amount) => {
    if (amount > adminStats.withdrawableBalance) {
      alert("Insufficient balance");
      return;
    }
    
    setAdminStats(prev => ({
      ...prev,
      withdrawableBalance: prev.withdrawableBalance - amount,
      totalWithdrawn: (prev.totalWithdrawn || 0) + amount
    }));
    
    alert(`Successfully withdrew $${amount} to your secured account`);
  };

  // Reveal contact only after payment
  const revealContact = (type, id) => {
    if (type === 'developer') {
      setDevelopers(devs => 
        devs.map(dev => 
          dev.id === id ? {...dev, contactHidden: false} : dev
        )
      );
    } else {
      setAdvertisers(advs => 
        advs.map(adv => 
          adv.id === id ? {...adv, contactHidden: false} : adv
        )
      );
    }
  };

  // Complete Admin Dashboard
  const AdminDashboard = () => (
    <div className="min-h-screen bg-gray-50">
      <nav className="bg-red-600 text-white p-4 flex justify-between items-center">
        <h2 className="text-2xl font-bold">Admin Dashboard - Lee's Money Adds</h2>
        <div className="flex space-x-4">
          <button 
            onClick={() => setCurrentView('featured-ads')}
            className="bg-red-700 px-4 py-2 rounded hover:bg-red-800 flex items-center"
          >
            <Activity className="w-4 h-4 mr-1" />
            Featured Ads
          </button>
          <button 
            onClick={handleLogout}
            className="bg-gray-800 px-4 py-2 rounded hover:bg-gray-900 flex items-center"
          >
            <LogOut className="w-4 h-4 mr-1" />
            Logout
          </button>
        </div>
      </nav>
      
      <div className="container mx-auto p-6">
        <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
          <div className="bg-white rounded-lg p-6 shadow border-t-4 border-blue-500">
            <div className="flex justify-between">
              <h3 className="font-bold text-gray-700 mb-2">Total Revenue</h3>
              <DollarSign className="text-blue-500" />
            </div>
            <p className="text-3xl font-bold">${adminStats.totalRevenue.toLocaleString()}</p>
          </div>
          
          <div className="bg-white rounded-lg p-6 shadow border-t-4 border-green-500">
            <div className="flex justify-between">
              <h3 className="font-bold text-gray-700 mb-2">Withdrawable</h3>
              <ArrowDownToLine className="text-green-500" />
            </div>
            <p className="text-3xl font-bold">${adminStats.withdrawableBalance.toLocaleString()}</p>
          </div>
          
          <div className="bg-white rounded-lg p-6 shadow border-t-4 border-purple-500">
            <div className="flex justify-between">
              <h3 className="font-bold text-gray-700 mb-2">Active Contracts</h3>
              <FileText className="text-purple-500" />
            </div>
            <p className="text-3xl font-bold">{adminStats.activeContracts}</p>
          </div>
          
          <div className="bg-white rounded-lg p-6 shadow border-t-4 border-yellow-500">
            <div className="flex justify-between">
              <h3 className="font-bold text-gray-700 mb-2">Platform Fee</h3>
              <Lock className="text-yellow-500" />
            </div>
            <p className="text-3xl font-bold">{adminStats.platformFee}%</p>
          </div>
        </div>
        
        {/* Fund Withdrawal Section */}
        <div className="bg-white rounded-lg shadow p-6 mb-6">
          <h3 className="text-xl font-bold mb-4 flex items-center">
            <ArrowDownToLine className="w-5 h-5 mr-2 text-green-600" />
            Secure Fund Withdrawal
          </h3>
          <div className="border rounded-lg p-4 bg-gray-50 mb-4">
            <p className="font-semibold text-gray-700">Withdrawable Balance: 
              <span className="text-2xl ml-2">${adminStats.withdrawableBalance.toLocaleString()}</span>
            </p>
          </div>
          
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <label className="block text-gray-700 mb-2">Amount to Withdraw ($)</label>
              <input 
                type="number" 
                className="w-full border rounded px-3 py-2"
                placeholder="Enter amount"
                min="100"
                step="100"
                max={adminStats.withdrawableBalance}
                id="withdrawAmount"
              />
            </div>
            <div>
              <label className="block text-gray-700 mb-2">Secure Account</label>
              <select className="w-full border rounded px-3 py-2">
                <option>•••• •••• •••• 1234 (Primary)</option>
                <option>•••• •••• •••• 5678 (Backup)</option>
              </select>
            </div>
          </div>
          
          <div className="mt-6">
            <button 
              onClick={() => {
                const amount = parseFloat(document.getElementById('withdrawAmount').value);
                if (amount && amount >= 100) {
                  withdrawFunds(amount);
                } else {
                  alert("Minimum withdrawal is $100");
                }
              }}
              className="bg-green-600 hover:bg-green-700 text-white px-6 py-3 rounded-lg font-bold"
            >
              Process Withdrawal
            </button>
            <p className="text-sm text-gray-500 mt-2">
              Funds will arrive in 1-3 business days. All transactions are secured with 256-bit encryption.
            </p>
          </div>
        </div>
        
        {/* Contract Monitoring */}
        <div className="bg-white rounded-lg shadow p-6 mb-6">
          <h3 className="text-xl font-bold mb-4">Active Contracts</h3>
          <div className="overflow-x-auto">
            <table className="w-full">
              <thead>
                <tr className="bg-gray-100">
                  <th className="py-2 px-4 text-left">ID</th>
                  <th className="py-2 px-4 text-left">Dev ID</th>
                  <th className="py-2 px-4 text-left">Adv ID</th>
                  <th className="py-2 px-4 text-left">Amount</th>
                  <th className="py-2 px-4 text-left">Fee</th>
                  <th className="py-2 px-4 text-left">Status</th>
                </tr>
              </thead>
              <tbody>
                {contracts.map(contract => (
                  <tr key={contract.id} className="border-b">
                    <td className="py-2 px-4 font-mono">LMA-{contract.id}</td>
                    <td className="py-2 px-4">
                      {contract.developerId}
                      <span className="text-xs text-gray-500 block">{contract.watermark}</span>
                    </td>
                    <td className="py-2 px-4">{contract.advertiserId}</td>
                    <td className="py-2 px-4">${contract.amount}</td>
                    <td className="py-2 px-4 text-red-600 font-bold">${contract.platformFee}</td>
                    <td className="py-2 px-4">
                      <span className="bg-yellow-100 text-yellow-800 px-2 py-1 rounded text-sm">
                        {contract.status}
                      </span>
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>
          </div>
          <div className="mt-4 p-4 bg-red-50 border border-red-200 rounded">
            <h4 className="font-bold text-red-700 flex items-center">
              <Shield className="w-4 h-4 mr-2" />
              Legal Protection
            </h4>
            <p className="text-sm text-red-600">
              Platform is not liable for contracts between parties. All agreements are strictly between developers and advertisers.
              Our role is limited to providing the platform and payment processing.
            </p>
          </div>
        </div>
        
        {/* Featured Ads Management */}
        <div className="bg-white rounded-lg shadow p-6">
          <h3 className="text-xl font-bold mb-4">
            <Star className="w-5 h-5 mr-2 text-yellow-500 inline" />
            Lee's Money Ads - Premium Slots
          </h3>
          <div className="mb-6 p-4 bg-yellow-50 border border-yellow-200 rounded-lg">
            <p className="text-yellow-700">
              <span className="font-bold">30% commission</span> on all featured ads. These appear on our homepage and get 5x more views.
            </p>
          </div>
          
          <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
            {adminStats.featuredSlots.map(slot => (
              <div key={slot.id} className="border rounded-lg p-4 bg-gradient-to-br from-yellow-50 to-amber-50">
                <div className="flex justify-between items-start">
                  <div>
                    <h4 className="font-bold text-gray-800">{slot.advertiser}</h4>
                    <p className="text-gray-600">Bid: ${slot.bid}/day</p>
                    <p className="text-gray-600">Duration: {slot.duration}</p>
                  </div>
                  <span className="bg-yellow-500 text-white px-2 py-1 rounded text-sm">Active</span>
                </div>
                <div className="mt-4 pt-4 border-t">
                  <p className="font-bold text-green-600">Commission: ${(slot.bid * 0.3).toFixed(2)}/day</p>
                </div>
              </div>
            ))}
          </div>
          
          <button className="bg-yellow-500 hover:bg-yellow-600 text-white px-4 py-2 rounded flex items-center">
            <Star className="w-4 h-4 mr-1" />
            Create New Ad Slot
          </button>
        </div>
      </div>
    </div>
  );

  // Add security disclaimer to other dashboards
  const DeveloperDashboard = () => (
    // ... existing developer dashboard
    <div className="mt-4 p-4 bg-blue-50 border border-blue-200 rounded">
      <h4 className="font-bold text-blue-700 flex items-center">
        <Lock className="w-4 h-4 mr-2" />
        Secure Transaction Notice
      </h4>
      <p className="text-sm text-blue-600">
        All payments must be processed through our platform. Any attempt to circumvent payment processing 
        will result in account termination. Contact details are revealed only after payment is secured.
      </p>
    </div>
  );

  // ... rest of the code (AdvertiserDashboard, LandingPage, etc)

  return (
    <div>
      {!isLoggedIn ? (
        <LandingPage />
      ) : userType === 'dev' ? (
        <DeveloperDashboard />
      ) : userType === 'advertiser' ? (
        <AdvertiserDashboard />
      ) : userType === 'admin' ? (
        <AdminDashboard />
      ) : (
        <LandingPage />
      )}
    </div>
  );
};


 ) ? (
<{npm run build}/>
<{npm install netlify-cli -gnetlify deploy}/>
