# make more powerful fiddler


### Task 1. hosts Switcher

* edit **CustomRules.js**, add menu and domain list
    
	    public static var  domain_list ={'www.cgwxyz.com':1};
	
	    RulesString("&Hosts-Switcher", true) 
        BindPref("fiddlerscript.ephemeral.HostsSwitcher")
        RulesStringValue(0,"Localhost", "127.0.0.1")
        RulesStringValue(1,"Local_251", "192.168.1.251")
        RulesStringValue(2,"Local_252", "192.168.1.252")
        public static var sHosts: String = null;


* edit **OnBeforeRequest** function
     
		static function OnBeforeRequest(oSession: Session) {
			if(null != sHosts){
                if(domain_list[oSession.host] == 1){
                    oSession.bypassGateway = true; // Prevent this request from going through an upstream proxy
                    oSession["x-overrideHost"] = sHosts;  // DNS name or IP address of target server
                    oSession.oRequest["byswitch"] = "hostsSwitcher_"+sHosts;
                }
            }
    	}

* done.A menu "Hosts-Switcher" will displayed under Rules.


### Task 2.Add Customize Column

* edit **CustomRules.js**,add

        public static BindUIColumn("HTTPMethod")
        function CalcMethodCol(oS: Session){
            if (null != oS.oRequest) return oS.oRequest.headers.HTTPMethod; else return String.Empty; 
        }
        
        public static BindUIColumn("Time Taken")
        function CalcTimingCol(oS: Session){
            var sResult = String.Empty;
            if ((oS.Timers.ServerDoneResponse > oS.Timers.ClientDoneRequest))
            {
                sResult = String.Format("{0:h:mm:ssss.ffff}", 
                    (oS.Timers.ServerDoneResponse - oS.Timers.ClientDoneRequest));
            }
            return sResult;
        }