# WebApp
<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="server_login.aspx.cs" Inherits="IIS._04.server_login" %>

<!DOCTYPE html>

<html xmlns="http://www.w3.org/1999/xhtml">
<head runat="server">
    <title></title>
</head>
<body style="height: 298px">
    <form id="form1" runat="server">
        <br />
        Usuario:&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
        <asp:TextBox ID="txtUsuario" runat="server" Width="137px"></asp:TextBox>
&nbsp;<p>
            Contraseña:&nbsp;&nbsp;
            <asp:TextBox ID="txtContraseña" runat="server" Width="138px"></asp:TextBox>
        </p>
        <p>
            Dominio:&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            <asp:TextBox ID="txtDominio" runat="server" Width="142px"></asp:TextBox>
        </p>
        <p>
            <asp:Button ID="btnInicio" runat="server" OnClick="btnIngresar_Click" Text="Inicio" />
        </p>
        <p>
            &nbsp;</p>
        <p>
            &nbsp;</p>
    </form>
</body>
</html>

using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using System.DirectoryServices;

namespace IIS._04
{
    public partial class server_login : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }
        
        protected void btnIngresar_Click(object sender, EventArgs e)
        {
            Usuario usuario = new Usuario();
            usuario.Autenticar(txtUsuario.Text, txtContraseña.Text, txtDominio.Text);
        }
    }
}

using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.DirectoryServices;
using System.Collections;
using System.Web.UI;

namespace IIS._04
{
    public class Usuario
    {
        private string Nombre { get; set; }
        private string Contraseña { get; set; }
        private string Dominio { get; set; }

        public Usuario()
        {
            this.Nombre="";
            this.Contraseña = "";
            this.Dominio = "";
        }
        
        public bool Autenticar(string Nombre, string Contraseña, string Dominio)
        {
            bool authentic = false;
            try
            {
                DirectoryEntry nuevaEntrada = new DirectoryEntry("LDAP://173.31.21.77" + Dominio, Nombre, Contraseña);
                object nativeObject = nuevaEntrada.NativeObject;
                authentic = true;
                
            }
            catch (DirectoryServicesCOMException) { }
            return authentic;
        }

        public ArrayList Grupos()
        {
            ArrayList groups = new ArrayList();
            foreach (System.Security.Principal.IdentityReference group in
                System.Web.HttpContext.Current.Request.LogonUserIdentity.Groups)
            {
                groups.Add(group.Translate(typeof
                    (System.Security.Principal.NTAccount)).ToString());
            }
            return groups;
        }
    }
}
