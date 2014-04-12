RSI-
====

RSI code
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.Data;
using System.Collections;

namespace WebApplication1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
            if (IsPostBack)a
            {

            }
            else
            {
                companynameslist.DataSource = WebApplication1.dbWrapper.GetAllCompanyNames("stock_data");
                companynameslist.DataTextField = "companyname";
                companynameslist.DataValueField = "companyname";
                companynameslist.DataBind();
            }
        }

        protected void companynameslist_SelectedIndexChanged(object sender, EventArgs e)
        {
            try
            {
                string companyname = companynameslist.SelectedValue;
                DataTable tbl = WebApplication1.dbWrapper.GetCompanyData("stock_data", companyname);
                TableRow header = new TableRow();
                TableCell dateColHeading = new TableCell(); dateColHeading.Text = "Date";
                header.Cells.Add(dateColHeading);
                TableCell openValueColHeading = new TableCell(); openValueColHeading.Text = "Open Value";
                header.Cells.Add(openValueColHeading);
                TableCell openRateColHeading = new TableCell(); openRateColHeading.Text = "Open Rate";
                header.Cells.Add(openRateColHeading);
                TableCell highRateColHeading = new TableCell(); highRateColHeading.Text = "Highh Rate";
                header.Cells.Add(highRateColHeading);
                TableCell lowRateColHeading = new TableCell(); lowRateColHeading.Text = "Low Rate";
                header.Cells.Add(lowRateColHeading);
                TableCell changingHeading = new TableCell(); changingHeading.Text = "Change";
                header.Cells.Add(changingHeading);
                TableCell gainHeading = new TableCell(); gainHeading.Text = "Gain";
                header.Cells.Add(gainHeading);
                TableCell lossHeading = new TableCell(); lossHeading.Text = "Loss";
                header.Cells.Add(lossHeading);
                TableCell avgGainHeading = new TableCell(); avgGainHeading.Text = "Avg Gain";
                header.Cells.Add(avgGainHeading);
                TableCell avgLossHeading = new TableCell(); avgLossHeading.Text = "Avg Loss";
                header.Cells.Add(avgLossHeading);
                TableCell RSHeading = new TableCell(); RSHeading.Text = "RS";
                header.Cells.Add(RSHeading);
                TableCell RSIHeading = new TableCell(); RSIHeading.Text = "RSI";
                header.Cells.Add(RSIHeading);
                Table1.Rows.Add(header);
                ArrayList avgGainArray = new ArrayList();
                ArrayList avgLossArray = new ArrayList();
                float gainedValue = 0;
                float lossedValue = 0;
                for (int i=0; i<tbl.Rows.Count;i++)
                {
                    string stockdate = tbl.Rows[i]["dateOfStock"].ToString();
                    float open_value = float.Parse(tbl.Rows[i]["open_value"].ToString());
                    float open_rate = float.Parse(tbl.Rows[i]["open_rate"].ToString());
                    float high_rate = float.Parse(tbl.Rows[i]["high_rate"].ToString());
                    float low_rate = float.Parse(tbl.Rows[i]["low_rate"].ToString());
                    TableRow r = new TableRow();
                    TableCell dateCol = new TableCell();
                    TableCell openValueCol = new TableCell();
                    TableCell openRateCol = new TableCell();
                    TableCell highRateCol = new TableCell();
                    TableCell lowRateCol = new TableCell();
                    TableCell changing = new TableCell();
                    TableCell gain = new TableCell();
                    TableCell loss = new TableCell();
                    TableCell avgGain = new TableCell();
                    TableCell avgLoss = new TableCell();
                    TableCell RS = new TableCell();
                    TableCell RSI = new TableCell();
                    dateCol.Text = stockdate;
                    openValueCol.Text = open_value.ToString();
                    openRateCol.Text = open_rate.ToString();
                    highRateCol.Text = high_rate.ToString();
                    lowRateCol.Text = low_rate.ToString();
                    if(i==0)
                    {
                        r.Cells.Add(dateCol);
                        r.Cells.Add(openValueCol);
                        r.Cells.Add(openRateCol);
                        r.Cells.Add(highRateCol);
                        r.Cells.Add(lowRateCol);
                    }
                    else
                    {
                        r.Cells.Add(dateCol);
                        r.Cells.Add(openValueCol);
                        r.Cells.Add(openRateCol);
                        r.Cells.Add(highRateCol);
                        r.Cells.Add(lowRateCol);
                        float low_rate_yesterday = float.Parse(tbl.Rows[i-1]["low_rate"].ToString());
                        float tempChange = low_rate - low_rate_yesterday;
                        TableCell tempChangeCell = new TableCell();
                        tempChangeCell.Text = "";
                        changing.Text = tempChange.ToString();
                        r.Cells.Add(changing);
                        if(tempChange < 0)
                        {
                            loss.Text = (-1*tempChange).ToString();
                            r.Cells.Add(tempChangeCell);
                            r.Cells.Add(loss);
                            avgGainArray.Add(0);
                            avgLossArray.Add((-1 * tempChange));
                            //array_push($avgLossArray,round(($data[$i-1]['close'] - $data[$i]['close']),2));
                            //array_push($avgGainArray,0);
                        }
                        else
                        {
                            gain.Text = tempChange.ToString();
                            r.Cells.Add(gain);
                            r.Cells.Add(tempChangeCell);
                            avgGainArray.Add(tempChange);
                            avgLossArray.Add(0);
                            //array_push($avgGainArray,round(($data[$i]['close'] - $data[$i-1]['close']),2));
                            //array_push($avgLossArray,0);
                        }
                    }
                    if (i < 14)
                    {
                        avgGain.Text = "";
                        r.Cells.Add(avgGain);
                        avgLoss.Text = "";
                        r.Cells.Add(avgLoss);
                        RS.Text = "";
                        r.Cells.Add(RS);
                        RSI.Text = "";
                        r.Cells.Add(RSI);
                    }
                    else if (i==14)
			        {
				        gainedValue = calcAvg(avgGainArray);
                        lossedValue = calcAvg(avgLossArray);
                        avgGain.Text = gainedValue.ToString();
                        r.Cells.Add(avgGain);
                        avgLoss.Text = lossedValue.ToString();
                        r.Cells.Add(avgLoss);
                        float tempRs = gainedValue / lossedValue;
                        RS.Text = tempRs.ToString();
                        r.Cells.Add(RS);
                        float tempRsi = 100 - (100 / (1 + tempRs));
                        RSI.Text = tempRsi.ToString();
                        r.Cells.Add(RSI);
			        }
                    else
                    {
                        float tempValueForGain = float.Parse(avgGainArray[i-1].ToString());
                        gainedValue = (((gainedValue * 13) + float.Parse(avgGainArray[i - 1].ToString())) / 14);
                        lossedValue = (((lossedValue * 13) + float.Parse(avgLossArray[i - 1].ToString())) / 14);
                        avgGain.Text = gainedValue.ToString();
                        r.Cells.Add(avgGain);
                        avgLoss.Text = lossedValue.ToString();
                        r.Cells.Add(avgLoss);
                        float tempRs = gainedValue / lossedValue;
                        RS.Text = tempRs.ToString();
                        r.Cells.Add(RS);
                        float tempRsi = 100 - (100 / (1 + tempRs));
                        RSI.Text = tempRsi.ToString();
                        r.Cells.Add(RSI);
                    }
                    Table1.Rows.Add(r);
                }

            }
            catch(Exception err){
                Console.WriteLine(err.Message);
            }

        }
        public float calcAvg(ArrayList data){
            float result = 0;
            
            foreach ( Object obj in data )
            {
                result += float.Parse(obj.ToString());
            }
            return result;
        }
    }
}
