# fiddler_tips
make powerful fiddler.


==hosts switcher

*edit CustomRules.js, add
    
    public static var  domain_list ={'www.cgwxyz.com':1};

    public static RulesOption("Localhost", "H&osts-Switcher") //add a menu under Rules
    var b_is_local: boolean = false;

    public static RulesOption("TestServer", "H&osts-Switcher")
    var b_is_test: boolean = false;


* edit OnBeforeRequest function ,add:
    static function OnBeforeRequest(oSession: Session) {
       if( b_is_local){ //localhost checked
            if(domain_list[oSession.host] == 1){
                    oSession.bypassGateway = true;                   // Prevent this request from going through an upstream proxy
                    oSession["x-overrideHost"] = "127.0.0.1";  // DNS name or IP address of target server
                    oSession.oRequest["byswitch"] = "hosts-switcher";//add a new request header to mark it.
            }
        }
        
        if(b_is_test){
            if(domain_list[oSession.host] == 1){
                    oSession.bypassGateway = true; 
                    oSession["x-overrideHost"] = "192.168.1.251";
                    oSession.oRequest["byswitch"] = "hosts-switcher";
            }
        }       
    }
