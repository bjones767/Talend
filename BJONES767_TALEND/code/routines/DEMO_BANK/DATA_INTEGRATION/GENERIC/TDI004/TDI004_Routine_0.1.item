package routines;

import java.io.File;
import java.io.IOException;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

import org.json.simple.JSONArray;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;

public class TDI004_Routine {

	/**
	 *	@param parentJSON "parentName":{ "parentFKey":"value", "childname":[{...}]}
	 *	@param childJSON  "childName":[{"childFKey":"value"},{...}]"
	 *	@param parentFKeyName The name of the parent key that will match the child key value
	 *	@param childFKeyName The name of the child key that matches the parent key value
	 *	@return String Final JSON 
	 */
    public static String updateLoopObject(String parentJSON, String childJSON, String parentFKeyName, String childFKeyName) throws Exception {
    	JSONObject parentJO = (JSONObject)(new JSONParser().parse(parentJSON));
    	JSONObject childJO = (JSONObject)(new JSONParser().parse(childJSON));
    	JSONArray childJA = new JSONArray();
    	String childMainObject = "";
    	Integer parentIndex = 0;
    	Iterator<Map.Entry>childEntryItr = childJO.entrySet().iterator();
		/*GET THE CHILD OBJECT FOR PROCESSING WHEN THE CHILD IS FOUND IN THE PARENT BELOW
		 */
    	while(childEntryItr.hasNext()) {
    		childMainObject = (String)childEntryItr.next().getKey();//detail
    		childJA = (JSONArray) childJO.get(childMainObject);
    		//Iterator childArrayIter = childJA.iterator();
    		//while(childArrayIter.hasNext()) {
    		//	System.out.println(((JSONObject)childArrayIter.next()).entrySet()); //[Trace_Number= 5, Amount= Jackson , Transaction_Code=2 , Detail_Transit= James ][...
    		//}
    	}
    	/*FIRST WE GET THE OUTER MOST ARRAY OF THE PARENT
		 */
    	Iterator<Map.Entry>parentEntryItr = parentJO.entrySet().iterator();
    	while(parentEntryItr.hasNext()) {
    		JSONArray parentJA = (JSONArray) parentJO.get(parentEntryItr.next().getKey());//batch_header
			/*GO THROUGH THE ARRAY AND RETRIEVE EACH OBJECT IN THAT OUTER ARRAY
			 */
    		Iterator parentArrayIter = parentJA.iterator();
    		while(parentArrayIter.hasNext()) {
    			parentIndex++;
    			JSONObject parentJOdata = (JSONObject)parentArrayIter.next();
    			//System.out.println(parentJOdata.entrySet());//[Service_Class_Code1=1 , Foreign_Exchange_Indicator= AL, detail=[]][...
    			String parentKeyValue = (String)parentJOdata.get(parentFKeyName);
				/*LOOP THROUGH THE ITEMS IN THE CURRENT OBJECT OF THE ABOVE LOOP
				 */
    			Iterator<Map.Entry>parentInternalItr = parentJOdata.entrySet().iterator();
    			while(parentInternalItr.hasNext()) { 
    				Map.Entry parentJO2 = parentInternalItr.next();
    				/*CHECK IF THE CHILD OBJECT NAME WAS FOUND IN THIS ITERATION
					 */
    				if(parentJO2.getKey().equals(childMainObject)) {//childObjectNameInParent == childObjectName?
    					JSONArray parentJAInner = (JSONArray)parentJO2.getValue();
						/*LOOP THROUGH ALL THE CHILDREN FROM THE ARRAY 
						 */
    					Iterator childArrayIter = childJA.iterator();
    		    		while(childArrayIter.hasNext()) {
    		    			JSONObject jo3 = (JSONObject)childArrayIter.next();
    		    			//System.out.println(jo3.entrySet());//[Trace_Number= 5, Amount= Jackson , Transaction_Code=2 , Detail_Transit= James ][...
							/*FOR EACH ITEM IN EACH CHILD OBJECT OF THE CURRENT ARRAY LOCATION 
							 */
    		    			Iterator<Map.Entry>jo3EntryIter = jo3.entrySet().iterator();
    		    			while(jo3EntryIter.hasNext()) {
    		    				Map.Entry childJOdata = jo3EntryIter.next();
								/*IF THE KEY OF THE CURRENT ITEM IS EQUAL TO THE PARAMETER childFKeyName && THE VALUES OF THE PARENT KEY AND CHILD KEY ARE EQUAL
								 *ADD THE CHILD OBJECT TO THE CURRENT PARENT
								 */
    		    				if(((String)childJOdata.getKey()).equals(childFKeyName)) {//Trace_Number == Service_Class_Code1?
    		    					//System.out.println(childFKeyName + " = " + (String)childJOdata.getValue() + " || "+ parentFKeyName + " = " + parentKeyValue + " || " + (parentKeyValue.equals((String)childJOdata.getValue())));
    		    					String p = parentKeyValue.trim();
    		    					String c = ((String)childJOdata.getValue()).trim();
    		    					if(p.equals(c)) {
    		    						parentJAInner.add(jo3);
    		    					}
    		    				}
    		    			}
    		    		}
    				}
    				
    			}
    		}
    	}
    	return parentJO.toJSONString();
    }
}