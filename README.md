# recaptchav3-login


    public function login(Request $request){
        // start reCaptcha for success Google
    	$vars = array(
            'secret' => env('G_RECAPTCHA_SECRET_KEY'),  // this part get secret key in .env
            "response" => $request->input('recaptcha_v3')
        );
        $url = "https://www.google.com/recaptcha/api/siteverify"; // this part is fix to relation google and our App
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $vars);
        $encoded_response = curl_exec($ch);
        $response = json_decode($encoded_response, true);
        curl_close($ch);
        // in this part if response of reCaptchs is correct do another function like Register
        if($request->input('recaptcha_v3')=='null'){
            return redirect()->back()->withErrors(['recaptcha_v3' => 'you are bot']);
        }
        elseif(!$response['success']){
            return redirect()->back()->withErrors(['recaptcha_v3' => 'you are bot']);
        }
        elseif(!$response['action'] == 'homepage'){
            return redirect()->back()->withErrors(['recaptcha_v3' => 'you are bot']);
        }
        // the end for Google success and put all of code about login here like this
        if($response['success']) {
        if($response['action'] == 'homepage'){ if($response['score']>0.5) {
            if($this->validate($request, [
                'email' => 'required|email',
                'password' => 'required|string|min:6',
            ])){
                $credentials = $request->only('email', 'password');
                if ($token = $this->guard()->attempt($credentials)) {
                    return view('home');
                }
            return redirect('home');
          }
        }}} else {
             return redirect()->back()->withErrors(['recaptcha_v3' => 'you are Robote']);
        }
        
        
       
    }
