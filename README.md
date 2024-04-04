%else %if %upcase(&extrap_depdmd.) = DEP_LOOP %then %do;
		%let sim_month = %eval(&sim_dur * 12); /* Total months in the simulation */

		data &outdsn1.;
		    set work.depdmdstaticdata_clean;
		    by NIIN Date;
		
		    array first_24_months{24} _temporary_;
		    retain month_id;
		
		    if first.NIIN then do;
				month_id = 0;
		    end;
	
			month_id + 1;
		
		    if month_id <= 24 then do;
		        first_24_months{month_id} = depdmdqty;
		    end;
		    else do;
		        depdmdqty = first_24_months{mod(month_id-1, 24) + 1};
		    end;
		
		    if month_id <= &sim_month then output; /* Ensure output up to the specified simulation duration */
		
		run;

	%end;
